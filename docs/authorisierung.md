# Authorisierung

Die API des Contentfly CMS basiert auf einer Token-Authentifizierung. Dieser Token kann
entweder manuell gesetzt, oder über ein Login (Benutzername und Passwort) dynamisch
geladeb werden.

## Login

### Seiten anlegen

* `ionic generate page loading`
* `ionic generate page login`
* `ionic generate page start`

### SDK initialisieren

**app/app.component.ts**
```
@Component({
  templateUrl: 'app.html'
})
export class MyApp {
  rootPage:any = 'LoadingPage';

  constructor(..., private contentflySdk : ContentflySdk) {
    
    platform.ready().then(() => {

      this.contentflySdk.setDebug(true);

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
  }
}
```

### Login

**pages/login/login.html**
```
<ion-header>
  <ion-navbar>
    <ion-title>Login</ion-title>
  </ion-navbar>
</ion-header>

<ion-content padding>
  <ion-list>

    <ion-item>
      <ion-label color="primary"  floating>Benutzername</ion-label>
      <ion-input type="text" [(ngModel)]="username" [disabled]="doLogin"></ion-input>
    </ion-item>

    <ion-item>
      <ion-label color="primary"  floating>Passwort</ion-label>
      <ion-input type="password" [(ngModel)]="password" [disabled]="doLogin"></ion-input>
    </ion-item>

  </ion-list>

  <button ion-button full (click)="login()" [disabled]="!username || !password || doLogin">
    <ion-spinner [hidden]="!doLogin" name="ios-small"></ion-spinner> Einloggen
  </button>
</ion-content>
```

**pages/login/login.ts**

```
export class LoginPage {

  doLogin : boolean = false;
  password : string = '';
  username : string = '';

  constructor(private contentflySdk : ContentflySdk, public navCtrl: NavController, public navParams: NavParams, private toastCtrl : ToastController) {
  }

  login(){
    this.doLogin = true;

    this.contentflySdk.login(this.username, this.password).then(() => {

      this.navCtrl.setRoot('StartPage');

    }).catch((message) => {
      this.doLogin = false;

      let toast = this.toastCtrl.create({
        message: message.error ? message.error.message : 'Der Server ist derzeit leider nicht erreichbar.',
        duration: 5000,
        position: 'center'
      });

      toast.present();
    });
  }  


}
```

### Logout

**pages/start/start.html**

```
<ion-header>
  <ion-navbar>
    <ion-title>Startseite</ion-title>
    <ion-buttons end>
      <button ion-button (click)="logout()">
        Ausloggen
      </button>
    </ion-buttons>
  </ion-navbar>
</ion-header>
```

**pages/start/start.ts**

```
export class StartPage {

  constructor(public navCtrl: NavController, public navParams: NavParams, private contentflySdk : ContentflySdk) {
  }

  logout(){
    this.contentflySdk.logout();
    this.navCtrl.setRoot('LoginPage');
  }

}
```

## Statischer Token

**app/app.component.ts**
```
@Component({
  templateUrl: 'app.html'
})
export class MyApp {
  rootPage:any = 'LoadingPage';

  constructor(..., private contentflySdk : ContentflySdk) {
    this.contentflySdk.setStaticToken(CONTENTFLY_TOKEN);
    
    platform.ready().then(() => {

      return this.contentflySdk.ready();

    }).then(() => {
      if(this.contentflySdk.lastSyncDate()){
        this.rootPage = 'StartPage';
      }else{
        this.rootPage = 'SyncPage';
      }
      
      statusBar.styleDefault();
      splashScreen.hide();
    });

  }
}
```

Im obigen Beispiel wird die Synchronisierung des Contentfly SDK verwendet und
beim Starg der App abgefragt, ob bereits Daten vorhanden sind. Wenn nicht
und die App zum ersten Mal gestartet wird, wird die Synchronisierungs-Seite
aufgerufen.

!!! note "Statischer Token vs Login"
    Da beim statischen Token dieser fest in der App hinterlegt werden  
    und bei Token-Änderungen (z.B. wegen Missbrauch) ein App-Update veröffentlicht
    werden muss, empfehlen wir wenn möglich immer die Login-Variante.