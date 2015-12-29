# Fregelet
A very basic web framework for Frege on top of Servlet

## Run via maven

1. `mvn install`
1. `mvn exec:exec`

## Example

### Start server

```frege
config = ServerConfig {
    port = 8080,

    routes = [
      ("/greet/:name", [Get, Post], greet),
      ("/date", [Get], date),
      ("/download", [Get], downloadCsv)
    ]
  }

main = startServer config
```
### Send JSON

```frege

data Greeting = Greeting { message :: String }

instance ToJSON Greeting where
    toJSON Greeting { message } = Struct [ assoc "message" message ]

greet :: Handler ()
greet = do
  name ← pathParam "name"
  let greeting = Greeting $ "Hello, " ++ (fromMaybe "there" name)
  sendJSON greeting

```

### Send simple text

```
date :: Handler ()
date = liftIO (Date.new () >>= _.toString) >>= sendText

```

### Write directly to response

```
downloadCsv :: Handler ()
downloadCsv = do
  web ← Handler.ask
  let response = web.response
      csvData = "foo,bar,baz"
      sendCsv = do
        response.setContentType "text/csv"
        response.setHeader "Content-Disposition" "attachment; fileName=output.csv"
        response.write csvData
  liftIO sendCsv


```
