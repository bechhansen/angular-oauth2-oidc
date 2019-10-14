# Archiving this repository: Original angular-oauth2-oidc now does codeflow. Will request to add contextual parameters.

# angular-oauth2-oidc-codeflow
[![Build Status](https://travis-ci.org/bechhansen/angular-oauth2-oidc.svg?branch=master)](https://travis-ci.org/bechhansen/angular-oauth2-oidc)


angular-oauth2-oidc-codeflow is an OAuth2 and OpenId Connect (OIDC) client for Angular.
The library is a Github fork of [manfredsteyer/angular-oauth2-oidc](https://github.com/manfredsteyer/angular-oauth2-oidc).
The code of this library is found at [bechhansen/angular-oauth2-oidc](https://github.com/bechhansen/angular-oauth2-oidc).

The fork extends the existing library so it do also support:
- [Authorization Code Grant flow](https://tools.ietf.org/html/rfc6749#page-24)


## Installing

```
npm i angular-oauth2-oidc-codeflow --save
```

## Importing the NgModule

```TypeScript
import { OAuthModule } from 'angular-oauth2-oidc-codeflow';
[...]

@NgModule({
  imports: [ 
    [...]
    HttpModule,
    OAuthModule.forRoot()
  ],
  declarations: [
    AppComponent,
    HomeComponent,
    [...]
  ],
  bootstrap: [
    AppComponent 
  ]
})
export class AppModule {
}

``` 

## Configuring for Code Grant Flow

To configure the library the following sample uses the new configuration API introduced with Version 2.1.
Hence, The original API is still supported.

```TypeScript
import { AuthConfig } from 'angular-oauth2-oidc-codeflow';

export const authConfig: AuthConfig = {

  // Url of the Identity Provider
  issuer: 'https://steyer-identity-server.azurewebsites.net/identity',

  // URL of the SPA to redirect the user to after login
  redirectUri: window.location.origin + '/index.html',

  // The SPA's id. The SPA is registerd with this id at the auth-server
  clientId: 'spa-demo',

  // set the scope for the permissions the client should request
  // The first three are defined by OIDC. The 4th is a usecase-specific one
  scope: 'openid profile email voucher',
}
```

Configure the OAuthService with this config object when the application starts up:

```TypeScript
import { OAuthService } from 'angular-oauth2-oidc';
import { JwksValidationHandler } from 'angular-oauth2-oidc';
import { authConfig } from './auth.config';
import { Component } from '@angular/core';

@Component({
    selector: 'flight-app',
    templateUrl: './app.component.html'
})
export class AppComponent {

    constructor(private oauthService: OAuthService) {
      this.configureWithNewConfigApi();
    }

    private configureWithNewConfigApi() {
      this.oauthService.configure(authConfig);
      this.oauthService.tokenValidationHandler = new JwksValidationHandler();
       
      this.oauthService.loadDiscoveryDocumentAndTryLogin().then(_ => {
        console.log("Logged in");
      }).catch(err => {
        console.log("Unable to login");
      })
      
      
      // Call this.oauthService.tryLogin() if discovery document is not used.
      // All configurations must be set manually.
    }
}
```

To initialize the login login flow, make a button call 'initAuthorizationCodeFlow()':
```TypeScript
this.oauthService.initAuthorizationCodeFlow();
```





