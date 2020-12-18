--Oauth2 Authorization Flow
How to test

1) Launch client web page first.
	http://localhost:8090/getEmployees


2) This page will call authorization server to get the code
	http://localhost:8080/oauth/authorize

3) Once got the code, then call authorization server again to get the authorization token
	http://localhost:8080/oauth/token

4) Then finally call resource server url
	http://localhost:8080/user/getEmployeesList

=============================================	
How about SSO(Single Sign On)?
SSO is mostly done in client app.
Are they using Oauth2 authorization flow too? 
Not quite, it is Implicit
Much simplied flow with OIDC (Open ID Connect)

Implicit flow, as said earlier, is designed for single-page Javascript apps. This flow is vastly similar to the Authorization Code flow, 
except for the part involving authorization code. Due to security concerns, in this flow the client no longer receives an authorization 
code from the authorization server; instead, after the user agent successfully transfers credentials, the authorization server returns 
access tokens directly to the client. Refresh tokens are not allowed in the Implicit flow.


they need 
	authorization url
	redirectUrl
	clientId
	scope
	
eg)
https://github.com/shaheershukur/Angular-Single-Sign-On-OAuth2-OIDC-/tree/master/src/app

a) sso.config.ts 
import { AuthConfig } from 'angular-oauth2-oidc';

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

s
b) app.component.ts
import { Component } from '@angular/core';
import { OAuthService, JwksValidationHandler } from 'angular-oauth2-oidc';
import { authConfig } from './sso.config';
import { filter } from 'minimatch';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {

  constructor(private oauthService:OAuthService){
    this.configureSingleSignOn();
  }

  configureSingleSignOn(){
    this.oauthService.configure(authConfig);
    this.oauthService.tokenValidationHandler = new JwksValidationHandler();
    this.oauthService.loadDiscoveryDocumentAndTryLogin();
  }

  login(){
    this.oauthService.initImplicitFlow();
  }
  
  logout(){
    this.oauthService.logOut();
  }

  get token(){
    let claims:any = this.oauthService.getIdentityClaims();
    return claims ? claims : null;
  }
}