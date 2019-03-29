# www - Chord Strategy - Product Site

- AuthN : Authentication: Proof someone is who they say they _are_.
  - Provides _identity_
- AuthZ : Authorization: Proof someone is allowed to _do_ what they have requested to _do_.
  - Provides _permission_

## Development

We're using Docker and Docker Compose to develop wordpress locally. This allows
a simple, repeatable, setup for all developers etc.

```sh
$ docker-compose up -d --build
```

### Links
- [Official Compose Docs on Wordpress](https://docs.docker.com/compose/wordpress/)
- Tutorials
  - [Simple](https://davidyeiser.com/tutorial/docker-wordpress-theme-setup)
  - [wp-cli](https://medium.com/@tatemz/using-wp-cli-with-docker-21b0ab9fab79)
- [Wordpress's DockerHub](https://hub.docker.com/_/wordpress/)

## Plugins
### Auth0

We use [Auth0](https://auth0.com/) to _authenticate any_ client/user _to_
Wordpress. We ***THEN*** use it to allow Users to authenticate with 3rd party
applications. Auth0, at the moment, does not provide our Authorization setup.

- [Auth0 Wordpress Plugin](https://auth0.com/wordpress)
  - https://auth0.com/docs/cms/wordpress/how-does-it-work
  - https://auth0.com/docs/cms/wordpress/configuration
- [Auth0 SAML](https://auth0.com/docs/protocols/saml/saml-idp-generic)

1.  install plugin, follow wizard
    - `Tenant Domain` is the Auth0 url. For us that is: `benicenow.auth0.com`
    - Steps to create an API Token:
      - https://auth0.com/docs/api/management/v2/tokens#get-a-token-manually
      - https://auth0.com/docs/api/management/v2/get-access-tokens-for-production
    - skipped the above, and instead just created a machine-to-machine app, and
      gave it _all_ of the possible permissions.
    - got the error:

> There was a problem with your log in: Could not create user. The registration process is not available. Please contact your site’s administrator. [error code: unknown]

Needed:

> Allow Signups: User signup will be available only if the WordPress Anyone can register option is enabled. You can find this setting under Settings > General > Membership.

2.  Remove access to logged in users to the WP dashboard
    - Need to install: https://wordpress.org/plugins/remove-dashboard-access-for-non-admins/
      - this removes _access_ but not the toolbar

3.  Remove the toolbar for all _non_ admins.
    - ie, just don't display the damn thing, it's ugly.
    - [Remove Toolbar for Non Admins](https://gist.github.com/nschaeferhoff/aeca664a7f82e77ce0b911994b5d0b55)
      - to edit that, go to themes -> editor

NOTE: WP does some checking of things, editing the `functions.php` file is
pretty borked. To get around this (without going through github setup, etc.)
simply deactivate the theme you _want_ to edit, _then_ edit it.

#### Auth0 -> Tableau

- [Tableau Online](https://auth0.com/docs/protocols/saml/saml-apps/tableau-online)
- [Tableau Server](https://auth0.com/docs/protocols/saml/saml-apps/tableau-server)

1.  Head to Tableau, start their steps:
    - enable an additional authentication type, SAML
    - https://us-east-1.online.tableau.com/#/site/chordtrial/authentication
2.  get our alias: `a822bb89-badc-4134-a8f1-1740aba719c6`
    - use the configuration for Auth0 found [here](https://auth0.com/docs/protocols/saml/saml-apps/tableau-online)
3.  follow the [steps for Auth0 SAML IdP](https://auth0.com/docs/protocols/saml/saml-idp-generic)
    1.  https://benicenow.auth0.com/samlp/fHY6yd3rceMbK112C1MxwMufchbMayg0
4.  when configuring the SAML `addon`, add in the configuration from above:
    - Callback is the ACS from Tableau
    - After you're all set, download the metadata xml
5.  upload the metadata xml from (4) to Tableau.

Attempted a login, but got a weird error detailing that the given User "was not
found". Not _sure_ if you can actually _create_ users via SAML in Tableau...

Additionally:

> User authentication through SAML does not apply to permissions and authorization for Tableau Online content, such as data sources and workbooks. It also does not control access to underlying data that workbooks and data sources connect to.

## Paid Member Subscriptions

https://www.cozmoslabs.com/wordpress-paid-member-subscriptions/

## Members

https://wordpress.org/plugins/members/

Useful for building more _roles_ into our WP installation.

## TODO

- https://wordpress.org/plugins/health-check/
