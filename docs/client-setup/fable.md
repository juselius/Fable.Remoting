# Client

On your client Fable project, you reference the shared API types:
```xml
<Compile Include="../Shared/SharedModels.fs" />
<Compile Include="Program.fs" />
```
Now you need to install the client package: [Fable.Remoting.Client](https://www.nuget.org/packages/Fable.Remoting.Client/):
```
dotnet add package Fable.Remoting.Client
```
### Proxy creation
Now that you have installed the package, you can use it to create a _proxy_: An object with which you are able to call the server in a type-safe manner: 
```fsharp
open ShareModels
open Fable.Remoting.Client

// musicStore : IMusicStore
let musicStore : IMusicStore = 
  Remoting.createApi()
  |> Remoting.buildProxy<IMusicStore>

async {
    let! albums = musicStore.allAlbums() 
    for album in albums do
        printfn "%s (%s)" album.Title album.Genre
}
|> Async.StartImmediate
```
### Developement server configuration
When you are working for example with `webpack-dev-server` in developement mode, you want to re-route the HTTP requests from your developement server to your backend, for that you must use the following configuration for webpack, assuming you are running `webpack-dev-server` on port 8080 and your backend is running on port 8083. You would change the `devServer` block:

```js
devServer: {
  contentBase: resolve('./public'),
  port: 8080
}
```
to this:
```js
devServer: {
  contentBase: resolve('./public'),
  port: 8080,
  // tell webpack-dev-server to re-route all requests 
  // from dev-server to the actual server
  proxy: {
    '/**': { 
      // assuming the suave server is running on port 8083
      target: "http://localhost:8083",
      changeOrigin: true
    }
}
```
This concept to any developement server you might be using to serve your client application.