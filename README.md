# freer-servant

A fully functional app template for starting a new [servant](https://hackage.haskell.org/package/servant) app with cookie authentication (via [servant-auth](https://hackage.haskell.org/package/servant-auth)), persistence via [postgresql-simple](https://hackage.haskell.org/package/postgresql-simple) and [postgresql-simple-migration](https://github.com/ameingast/postgresql-simple-migration). It uses [freer-simple](https://hackage.haskell.org/package/freer-simple) to decompose the API into business logic and wiring.

This is the result of my own Haskell learning experience - reviews, helpful suggestions & pull requests are welcome!


# Instructions

Prerequisites: Install [stack](<https://docs.haskellstack.org/en/stable/README/>) and have a PostgreSQL database named 'webapp' running on port 5432 (see App.hs for additional configuration parameters).


## Starting the server

Starting the server using stack:

```bash
stack setup
stack run
```

Starting the server using nix:

```bash
nix-shell
cabal new-run
```


## Testing the API:

```bash
# create a new user
curl -X POST -v -H "Content-Type: application/json" -d '{"_credentialsEmail":"user@example.com", "_credentialsPassword":"a password"}' localhost:4000/user
```

```bash
curl -X POST -b cookies -c cookies -v -H "Content-Type: application/json" -d '{"_credentialsEmail":"user@example.com", "_credentialsPassword":"a password"}' localhost:4000/session
```

```bash
# access the protected user endpoint, which returns the user as a JSON object
# Note that servant-auth uses XSRF protection, so you need to set a header field (it only works once, as the xsrf cookie is renewed after each request
curl -b cookies -c cookies -v -H "Content-Type: application/json" -H "X-XSRF-TOKEN: <enter xsrf token from cookies file here>" localhost:4000/user
```

```
# log out (doesn't work yet)
curl -X DELETE -b cookies -c cookies -v -H "Content-Type: application/json" localhost:4000/session
```

```bash
# verify the user endpoint is not accessible anymore
curl -b cookies -c cookies -v -H "Content-Type: application/json" localhost:4000/user
```
