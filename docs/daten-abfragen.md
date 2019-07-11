# Daten abfragen

## Lokale Daten per SQLite

Auf die synchronisierten Offline-Daten kann über SQL-Abfragen an die lokale
SQLite-Datenbank zugegriffen werden.

### Datenbank-Abfrage

**pages/list/list.html**
```
<ion-header>

  <ion-navbar>
    <ion-title>Personen</ion-title>
  </ion-navbar>

</ion-header>

<ion-content>

  <ion-list *ngIf="!isLoading">
    <ion-item *ngFor="let person of persons" (click)="openDetails(person)">{{person.firstname}} {{person.lastname}}</ion-item>
  </ion-list>
  
  <div *ngIf="isLoading">
    <p  text-center><ion-spinner class="button-spinner"></ion-spinner></p>
    <p  text-center>Daten werden geladen...</p>
  </div>

</ion-content>
```

**pages/list/list.ts**
```
export class ListPage {

  isLoading : boolean = true;
  persons : any[]     = []; 

  constructor(private contentflySdk : ContentflySdk, public navCtrl: NavController, public navParams: NavParams, private toastCtrl : ToastController) {
    this.loadData();
  }

  loadData(){
    this.isLoading = true;
    
    let statement = "SELECT * FROM persons WHERE age > ?";
    let params    = [18];
    
    this.contentflySdk.query(statement, params).then((persons) => {
      this.persons   = persons;
      this.isLoading = false;
    }).catch((error) => {
      console.log(JSON.stringify(error));
      this.isLoading = false;
    });    
  }
}
```

### Bilder anzeigen

Bilder aus der Tabelle pim_file können wie folgt angezeigt werden. Es reicht die ID des Bildes zur Ausgabe.

**pages/image/image.html**

```
<ion-header>

  <ion-navbar>
    <ion-title>Bildanzeige</ion-title>
  </ion-navbar>

</ion-header>


<ion-content padding>
  <div [ngStyle]="{'background-image': 'url(' + image(object.image_id) + ')'}">
    <img [src]="image(object.image_id)">
  </div>
</ion-content>
```

**pages/image/image.ts**

```
import { Component } from '@angular/core';
import { IonicPage, NavController, NavParams } from 'ionic-angular';
import {File} from "@ionic-native/file";

declare let window: any;

@IonicPage()
@Component({
  selector: 'page-image',
  templateUrl: 'image.html',
})
export class ImagePage {
    
  object : any = {};
      
  constructor(private file : File, public navCtrl: NavController, public navParams: NavParams) {
     this.object = this.navParams.get('object');
  }

  image(id : string){
    let path = this.file.dataDirectory + id;
    return window.Ionic.WebView.convertFileSrc(path);
  }

}
```

## Online-Daten über API

### api/list

Über [api/list](https://www.contentfly-cms.de/docs/cms/schnittstelle/list/) basiert der Zugriff, die Abfrage und die Rückgabe auf den ORM-Entitäten.

```
let params = {
  "entity": "Persons"
}

this.contentflySdk.api.post('list' params).then((response) => {
  this.persons = response['data'];
}).catch((error) => {
  console.log(JSON.stringify(error));
});
```

### api/single

Über [api/single](https://www.contentfly-cms.de/docs/cms/schnittstelle/single/) basiert der Zugriff, die Abfrage und die Rückgabe auf den ORM-Entitäten.

```
let params = {
  "entity": "Persons"
  "id": 223
}

this.contentflySdk.api.post('single' params).then((response) => {
  this.persons = response['data'];
}).catch((error) => {
  console.log(JSON.stringify(error));
});
```

### api/query

Über [api/query](https://www.contentfly-cms.de/docs/cms/schnittstelle/query/) kann beliebig auf die zu Grunde liegende Datenbank über Doctrine DBAL-Syntax zugegriffen werden.

```
let params = {
  "select": "*",
  "from": "persons",
  "where": {"age > ?", 18}
}

this.contentflySdk.api.post('query' params).then((response) => {
  this.persons = response['data'];
}).catch((error) => {
  console.log(JSON.stringify(error));
});
```

!!! tip "API-Dokumentation"
    Die API-Dokumentation zum Contentfly CMS finden Sie unter [www.contentfly-cms.de/docs/cms/schnittstelle/einfuehrung](https://www.contentfly-cms.de/docs/cms/schnittstelle/einfuehrung)

