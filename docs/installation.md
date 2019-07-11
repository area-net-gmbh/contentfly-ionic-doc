# Installation

## Vorbereitung

### Contentfly CMS

#### CORS-Einstellungen
Um das Contentfly SDK in Ionic nutzen zu können, müssen im Contentfy CMS Backend die CORS-Einstellungen konfiguriert werden.

**custom/config.php**

```
<?php
$configDefault->APP_ALLOW_ORIGIN            = '*';
$configDefault->APP_ALLOW_HEADERS           = 'content-type, x-xsrf-token, appcms-token, authorization';
$configDefault->APP_ALLOW_CREDENTIALS       = true;
```

#### Benutzer und Gruppe

* Es muss ein Benutzer angelegt und sinnvollerweise einer Gruppe zugeordnet sein
* Für die Benutzergruppe müssen folgende Einstellungen gesetzt sein:
    * Zugriff auf api/query erlauben = JA
    * Token-Timeout = 0 _(empfohlen)_
    * Zugriff auf die gewünschten Entitäten setzen

### Ionic installieren

Zu Beginn muss Ionic und Cordova auf dem Entwicklungsrechner installiert sein:

```
npm install -g ionic cordova
```

### App initialisieren

Die Ionic-App muss in der Version 3 initalisiert werden, das erreichen Sie mit dem aktuellen Ionic CLI
über die Angabe des Typs ionic-angular:

```
ionic start APPNAME --type=ionic-angular
```

!!! note "Ionic 3 vs Ionic 4"
    Das Contentfly SDK unterstützt derzeit lediglich Ionic 3, die Version 4 wird noch nicht unterstützt.

### Plattformen hinzufügen

Plattformen - je nach Bedarf - hinzufügen:

```
ionic cordova platform add ios
ionic cordova platform add android
```


## Contentfly SDK installieren

### NPM-Paketmanager

Das Contentfly Ionic SDK ist über den Paketmanager npm als Modul installierbar.

`npm install --save contentfly-ionic`

### Abhängigkeiten installieren

Folgende Module müssen zusätzlich installiert werden:

**SQLite**

```
ionic cordova plugin add cordova-sqlite-storage
npm install --save @ionic-native/sqlite@4
```

**File**

```
ionic cordova plugin add cordova-plugin-file
npm install --save @ionic-native/file@4
```

!!! note "Ionic 3 vs Ionic 4"
    Alle Ionic Native Plugins müssen für Ionic 3 in der Version „@4“installiert werden. Ohne diese Angabe
    werden inzwischen die Plugins für die 4er-Version standardmäßig installiert.


### Contentfly SDK im App-Hauptmodul hinzufügen

Um das Contentfly Ionic SDK innerhalb des eigenen Projektes nutzen zu können,
muss es in der _app.module.ts_ bekannt gemacht werden. Für zusätzliche Konfigurationseinstellungen empfehlen wir
das Anlegen einer separaten Konfigurationsdatei, z.B. _app/config.ts_:

**app/app.module.ts**

``` hl_lines="4 5 14 15 16 23"
import { BrowserModule } from '@angular/platform-browser';
import { IonicApp, IonicModule } from 'ionic-angular';
import { MyApp } from './app.component';
import {ContentflyModule, ContentflySdk} from "contentfly-ionic";
import {CONTENTFLY_URL} from "./config";

@NgModule({
  declarations: [
    MyApp
  ],
  imports: [
    BrowserModule,
    IonicModule.forRoot(MyApp, {}),
    ContentflyModule.forRoot({
      baseUrl: CONTENTFLY_URL
    })
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    MyApp
  ],
  providers: [
    ContentflySdk,
    ...
  ]
})
export class AppModule {}

```

**app/config.ts**

```
export const CONTENTFLY_URL : string = 'https://app.domain.de;
```



# Hinweise

Folgende Abhängigkeiten werden unter anderem über das Modul mitinstalliert und unterstützt:

* Angular:_ 5.2.9 bis < 6.0.0_
* Angular HttpClient:_ 5.2.9 bis < 6.0.0_
* Ionic Native _4.11.0 bis < 5.0.0_
* Ionic Storage _2.1.3 bis < 3.0.0_
* ionic-angular: _3.9.2 bis < 4.0.0_
* cordova-plugin-file: _6.0.1 bis < 7.0.0_
* cordova-sqlite-storage: _2.3.0 bis < 3.0.0_