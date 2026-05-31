# GoogleMapsLocationLab

> Application Android en Java basée sur Google Maps et la localisation.
> Le lab a pour objectif d’afficher une carte Google Maps, demander la permission de localisation, écouter les changements de position et afficher un marker sur la position actuelle.

![Platform](https://img.shields.io/badge/platform-Android-green)
![Language](https://img.shields.io/badge/language-Java-orange)
![SDK](https://img.shields.io/badge/minSdk-24-blue)
![Component](https://img.shields.io/badge/component-Google%20Maps-purple)
![Status](https://img.shields.io/badge/status-Not%20fully%20completed-red)

## Objectif du lab

Ce lab consiste à développer une application Android capable de :

* afficher une Google Map dans l’application ;
* demander la permission de localisation ;
* écouter les changements de position avec `LocationManager` ;
* utiliser `NETWORK_PROVIDER` ou `GPS_PROVIDER` ;
* ajouter un marker sur la position détectée ;
* zoomer automatiquement sur la position ;
* afficher une boîte de dialogue si le GPS est désactivé ;
* gérer la réponse de permission avec `onRequestPermissionsResult()`.

## État du lab

Le projet Android a été préparé, mais le lab n’a pas pu être finalisé complètement.

La raison est que l’affichage réel de Google Maps nécessite une clé **Google Maps API** valide. Pour générer cette clé, Google Cloud demande l’activation de la facturation. Je n’ai pas pu finaliser cette étape parce que je ne dispose pas d’une carte bancaire pour activer la facturation Google Cloud.

Donc :

* le projet Android peut être créé et compilé ;
* la structure du lab est comprise ;
* le code de permission et de localisation peut être préparé ;
* mais la carte Google Maps ne peut pas être affichée correctement sans clé API valide.

## Blocage rencontré

Le blocage principal est lié à la clé API Google Maps.

Sans clé API valide, l’application peut afficher :

```text
For development purposes only
```

ou une carte blanche.

Cela ne vient pas forcément du code Android, mais de l’absence d’une clé API Google Maps valide et configurée.

## Architecture prévue

Structure générale attendue :

```text
app/src/main/java/com/example/googlemapslocationlab/
│
└── MapsActivity.java

app/src/main/res/layout/
│
└── activity_maps.xml

app/src/main/res/values/
│
└── google_maps_api.xml
```

## Fichiers principaux

| Fichier               | Rôle                                            |
| --------------------- | ----------------------------------------------- |
| `MapsActivity.java`   | Gère la carte, la permission et la localisation |
| `activity_maps.xml`   | Contient le fragment Google Map                 |
| `google_maps_api.xml` | Contient la clé Google Maps API                 |
| `AndroidManifest.xml` | Déclare les permissions et la clé API           |
| `build.gradle.kts`    | Contient les dépendances Google Maps            |

## Permissions nécessaires

Dans `AndroidManifest.xml`, les permissions suivantes sont nécessaires :

```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.INTERNET" />
```

## Dépendances prévues

Les dépendances principales sont :

```kotlin
implementation("com.google.android.gms:play-services-maps:18.2.0")
implementation("com.google.android.gms:play-services-location:21.0.1")
```

## Clé Google Maps API

Le fichier suivant doit contenir une vraie clé API :

```text
app/src/main/res/values/google_maps_api.xml
```

Exemple :

```xml
<string name="google_maps_key" templateMergeStrategy="preserve" translatable="false">
    YOUR_GOOGLE_MAPS_API_KEY
</string>
```

Dans ce projet, aucune vraie clé API n’est publiée pour des raisons de sécurité.

## Fonctionnement attendu si la clé API est disponible

Si une clé API valide est ajoutée, l’application doit fonctionner comme suit :

1. L’utilisateur lance l’application.
2. La carte Google Maps s’affiche.
3. L’application demande la permission de localisation.
4. Si l’utilisateur accepte, l’application écoute les changements de position.
5. Un marker est placé sur la position actuelle.
6. La caméra zoome automatiquement sur la position.
7. Si le GPS est désactivé, une boîte de dialogue propose d’ouvrir les paramètres de localisation.

## Logique de localisation prévue

La localisation repose sur `LocationManager`.

Providers utilisés :

| Provider           | Description                                         |
| ------------------ | --------------------------------------------------- |
| `NETWORK_PROVIDER` | Utilise Wi-Fi ou réseau mobile, souvent plus rapide |
| `GPS_PROVIDER`     | Plus précis mais dépend du signal GPS               |

## Gestion du GPS désactivé

Une méthode `buildAlertMessageNoGps()` est prévue pour afficher une boîte de dialogue lorsque la localisation est désactivée.

Elle propose à l’utilisateur :

* `Yes` : ouvrir les paramètres de localisation ;
* `No` : fermer la boîte de dialogue.

## Gestion des markers

Deux approches sont possibles :

### 1. Ajouter un marker à chaque position

Cette méthode montre l’historique des positions, mais peut créer beaucoup de markers.

### 2. Déplacer un seul marker

Méthode recommandée :

```java
private Marker currentMarker;
```

À chaque changement de position :

```java
if (currentMarker == null) {
    currentMarker = mMap.addMarker(new MarkerOptions().position(pos).title("Position actuelle"));
} else {
    currentMarker.setPosition(pos);
}

mMap.animateCamera(CameraUpdateFactory.newLatLngZoom(pos, 15f));
```

Cette méthode est plus propre car elle évite de surcharger la carte.

## Tests prévus

| Test                       | Résultat attendu                   |
| -------------------------- | ---------------------------------- |
| Build du projet            | Le projet compile                  |
| Lancement de l’application | L’activité Maps s’ouvre            |
| Permission localisation    | Une demande de permission apparaît |
| Permission acceptée        | La localisation est activée        |
| Position détectée          | Un marker apparaît                 |
| Changement de position     | Le marker se déplace               |
| GPS désactivé              | Une boîte de dialogue s’affiche    |
| Caméra                     | La carte zoome sur la position     |

## Problèmes fréquents

| Problème                                | Cause possible                                           |
| --------------------------------------- | -------------------------------------------------------- |
| Carte blanche                           | Clé API absente ou incorrecte                            |
| Message “For development purposes only” | Clé API non valide ou facturation non activée            |
| Aucun marker                            | Permission refusée ou localisation désactivée            |
| Position ne change pas                  | Distance minimale trop grande ou provider inactif        |
| Crash `SecurityException`               | Permission non vérifiée avant `requestLocationUpdates()` |

## Limitation du rendu

Le lab n’a pas pu être démontré avec une vraie carte Google Maps, car l’obtention d’une clé API nécessite une configuration Google Cloud avec facturation.

Le projet est donc fourni comme base Android prête à compléter une fois une clé API valide disponible.

## Conclusion

Ce lab permet de comprendre l’intégration de Google Maps dans une application Android Java, la gestion des permissions de localisation et le suivi de position.

Même si la démonstration complète n’a pas pu être réalisée à cause de l’absence de clé API Google Maps valide, la structure du projet, les permissions nécessaires, la logique de localisation et les problèmes fréquents ont été étudiés.
