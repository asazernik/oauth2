# oauth2

Allows your Martini application to support user login via an OAuth 2.0 backend. Requires [`sessions`](https://github.com/martini-contrib/sessions) middleware. Google and Github sign-in are currently supported, once endpoints are provided, this middleware can work with any OAuth 2.0 backend.

## Usage

~~~ go
import (
  "github.com/codegangsta/martini"
  "github.com/martini-contrib/oauth2"
)

func main() {
  m := martini.Classic()
  m.Use(sessions.Sessions("my_session", sessions.NewCookieStore([]byte("secret123"))))
  m.Use(oauth2.Google(&oauth2.Options{
    ClientId:     "client_id",
    ClientSecret: "client_secret",
    RedirectURL:  "redirect_url",
    Scopes:       []string{"https://www.googleapis.com/auth/drive"},
  }))
  // tokens are injected to the handlers
  m.Get("/access_token", func(tokens Tokens) (int, string) {
    if tokens != nil {
      return 200, tokens.AccessToken()
    }
    return 403, "not authenticated"
  })
  m.Run()
}
~~~

## Auth flow

* /login will redirect user to the OAuth 2.0 provider's permissions dialog. If there is a `next` query param provided, user is redirected to the next page afterwards.
* If user agrees to connect, OAuth 2.0 provider will redirect to /oauth2callback to let your app to make the handshake. You need to register /oauth2callback as a Redirect URL.
* /logout will log the user out. If there is a `next` query param provided, user is redirected to the next page afterwards.
 
You can customize the login, logout, oauth2callback and error paths:

~~~ go
oauth2.PathLogin = "/oauth2login"
oauth2.PathLogout = "/oauth2logout"
...
~~~

## Authors

* [Burcu Dogan](http://github.com/rakyll)