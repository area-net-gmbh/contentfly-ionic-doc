# Synchronisation

Das Contentfly SDk synchronisert automatisiert die Datenbankstruktur und
Datenbankinhalte in eine lokale SQLite-Datenbank. Dateien werden ebenfalls
in das lokale Dateisystem (_ApplicationData-Directory_) synchronisiert.

## Grundlegende Einbindung

### Seiten anlegen

* `ionic generate page sync`

!!! tip "Synchronisation"
    Die Synchronisation kann natürlich auch in jeder anderen Seite aufgerufen
    oder in _app.component.ts_ über z.B. [ionicframework.com/docs/v3/api/platform/Platform/](https://ionicframework.com/docs/v3/api/platform/Platform/)

### Implementation

**pages/sync/sync.html**
```
<ion-header>

  <ion-navbar>
    <ion-title>Synchronisierung</ion-title>
  </ion-navbar>

</ion-header>

<ion-content padding>

<div padding *ngIf="message">
  <p text-center><ion-spinner name="ios"></ion-spinner></p>
  <h3 text-center ><b>{{message.text}}</b></h3>
  <p text-center *ngIf="message.all"><b>{{message.progress}}% synchronisiert</b><br>{{message.current}} von {{message.all}}</p>
</div>

</ion-content>
```

**pages/sync/sync.ts**
```
export class SyncPage {

  message : Message = null; 

  constructor(private contentflySdk : ContentflySdk, public navCtrl: NavController, public navParams: NavParams, private toastCtrl : ToastController) {
    this.startSync();
  }

  startSync(){

    this.contentflySdk.sync().subscribe(
      (message) => {
        this.message = message;
      },
      (error) => {
        //Fehlermeldung ausgeben, z.B. ToastController
      },
      () =>{
        this.navCtrl.setRoot('StartPage');
      }
    );
  }
}
```

## Methoden

### Beidseitige Synchronisation

Bei der beidseitigen Synchronisation werden zuerst alle offenen Datensätze
zum Server synchronisiert, erst danach Änderungen vom Server in die App
geladen. Schlägt beim Synchronisieren zum Server etwas fehl, werden aus
Sicherheitsgründen (damit keine lokalen Änderungen überschrieben werden)
auch keine Daten vom Server geladen.

```
this.contentflySdk.sync().subscribe(
  (message) => {
    
  },
  (error) => {
     
  },
  () =>{
    
  }
);
```

### Daetn zum Server synchronisieren
```
this.contentflySdk.syncTo().subscribe(
  (message) => {
    
  },
  (error) => {
     
  },
  () =>{
    
  }
);
```

### Daten vom Server synchronisieren
```
this.contentflySdk.syncFrom().subscribe(
  (message) => {
    
  },
  (error) => {
     
  },
  () =>{
    
  }
);
```

## Anmerkungen

### Bildgrößen

Im Standard werden immer die Originalbilder vom Server synchronisert. Meistens
reicht aber eine skalierte Ansicht (z.B. 960 Pixel Breite) aus.

Dazu muss im Contentfly CMS die Bildgröße unter "Administration->Bildgrößen"
angelegt und der verwendete Alias im SDK gesetzt werden.


**app/app.component.ts**
```
platform.ready().then(() => {

  this.contentflySdk.setDebug(true);
  this.contentflySdk.setImageDownloadSize('960px');
    
  return this.contentflySdk.ready();
}).then(() => {
  if(this.contentflySdk.user.exists()){
    this.rootPage = 'StartPage';
  }else{
    this.rootPage = 'LoginPage';
  }
  statusBar.styleDefault();
  splashScreen.hide();
})
```