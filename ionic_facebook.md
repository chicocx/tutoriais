# Ionic com login no Facebook

Após criar o projeto, execute os comandos no diretório do mesmo

```bash
ionic cordova plugin add cordova-plugin-facebook4 --variable APP_ID="2139537992723209" --variable APP_NAME="Beaut"
npm install --save @ionic-native/facebook
```

Depois modifique o arquivo **config.xml** 

```xml
<widget id="seu.pacote.do.app" version="0.0.1" xmlns="http://www.w3.org/ns/widgets" xmlns:cdv="http://cordova.apache.org/ns/1.0">
...
</widget>
```

Confira o arquivo **platforms\android\app\src\main\res\values\strings.xml** com o conteúdo:

```xml
    <string name="fb_app_id">123456789098762</string>
    <string name="fb_app_name">nome do app</string>
```

Crie a tela de login

```bash
ionic generate page login
```

Edite o arquivo login.ts

```typescript
import { Component } from '@angular/core';
import { IonicPage, NavController, NavParams } from 'ionic-angular';
import { Facebook } from '@ionic-native/facebook';

/**
 * Generated class for the LoginPage page.
 *
 * See https://ionicframework.com/docs/components/#navigation for more info on
 * Ionic pages and navigation.
 */

@IonicPage()
@Component({
  selector: 'page-login',
  templateUrl: 'login.html',
})
export class LoginPage {

  isLoggedIn:boolean = false;
  users: any;

  constructor(public navCtrl: NavController, public navParams: NavParams, private fb: Facebook) {
    fb.getLoginStatus()
    .then(res => {
      console.log(res.status);
      if(res.status === "connect") {
        this.isLoggedIn = true;
      } else {
        this.isLoggedIn = false;
      }
    })
    .catch(e => console.log(e));
  }

  ionViewDidLoad() {
    console.log('ionViewDidLoad LoginPage');
  }

  login() {
    this.fb.login(['public_profile', 'user_friends', 'email'])
      .then(res => {
        if(res.status === "connected") {
          this.isLoggedIn = true;
          this.getUserDetail(res.authResponse.userID);
        } else {
          this.isLoggedIn = false;
        }
      })
      .catch(e => console.log('Error logging into Facebook', e));
  }

  logout() {
    this.fb.logout()
      .then( res => this.isLoggedIn = false)
      .catch(e => console.log('Error logout from Facebook', e));
  }

  getUserDetail(userid) {
    this.fb.api("/"+userid+"/?fields=id,email,name,picture,gender",["public_profile"])
      .then(res => {
        console.log(res);
        this.users = res;
      })
      .catch(e => {
        console.log(e);
      });
  }

}

```

Edite o arquivo login.html

```html
<!--
  Generated template for the LoginPage page.

  See http://ionicframework.com/docs/components/#navigation for more info on
  Ionic pages and navigation.
-->
<ion-header>

  <ion-navbar>
    <ion-title>login</ion-title>
  </ion-navbar>

</ion-header>

<ion-content padding>
  <h1>TEste</h1>
  <div *ngIf="isLoggedIn; else facebookLogin">
    <h2>Hi, {{users.name}} ({{users.email}})</h2>
    <p>
      Gender: {{users.gender}}
    </p>
    <p>
      <img src="{{users.picture.data.url}}" width="100" alt="{{users.name}}" />
    </p>
    <p>
      <button ion-button icon-right (click)="logout()">
        Logout
      </button>
    </p>
  </div>
  <ng-template #facebookLogin>
    <button ion-button icon-right (click)="login()">
      Login with
      <ion-icon name="logo-facebook"></ion-icon>
    </button>
  </ng-template>


</ion-content>

```
Adicione no arquivo tabs.ts

```typescript
tabLogin = LoginPage;
```

Adicione no arquivo tabs.html
```html
<ion-tab [root]="tabLogin" tabTitle="Login" tabIcon="lock"></ion-tab>
```

Configure os módulos em **src\app\app.module.ts**


```typescript
@NgModule declarations -> LoginPage
@NgModule entryComponents -> LoginPage
@NgModule providers -> Facebook
```

## Gere os certificados
keytool -exportcert -alias beaut -keystore .\keystore.jks | openssl sha1 -binary | openssl base64
keytool -exportcert -alias beaut -keystore .\keystore.jks | openssl sha1 -binary | openssl base64
