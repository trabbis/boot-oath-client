*** OAuth 2.0 specification defines 4 types of authorization flows ***
  Authorization Code
  Resource Owner Password Credentials
  Implicit
  Client Credentials
  

2. This is the example of Oauth2 Authorization Flow
How to test
ref)
https://www.javainuse.com/spring/spring-boot-oauth-access-token


1) Launch client web page first.
	http://localhost:8090/getEmployees


2) This page will call authorization server to get the code
	http://localhost:8080/oauth/authorize

	use javainuse/javainuse
	

3) Once got the code, then call authorization server again to get the authorization token
	http://localhost:8080/oauth/token

4) Then finally call resource server url
	http://localhost:8080/user/getEmployeesList

=============================================	
1. How about SSO(Single Sign On)?
SSO is mostly done in client app. Very simple OAuth2 solution with OIDC (OpenID Connect)

they need 
	authorization url (Url of the Identity Provider (IDP)) ... see below example
	redirectUrl
	clientId
	scope
	
eg)
https://github.com/shaheershukur/Angular-Single-Sign-On-OAuth2-OIDC-/tree/master/src/app

a) sso.config.ts 
import { AuthConfig } from 'angular-oauth2-oidc';

export const authConfig: AuthConfig = {

  // Url of the Identity Provider (IDP)
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

TODO... check React solution if you have a minute

