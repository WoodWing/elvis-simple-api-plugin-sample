# Elvis DAM Simple API Plugin Sample

This is an easy to use sample Elvis DAM API plugin showing the concept, installation and usage. 

The sample creates an API proxy from the Elvis Server to a JSON test API, provided by https://jsonplaceholder.typicode.com/.

# Prerequisites

The sample requires:

* Fully installed and licensed [Elvis DAM server](https://www.woodwing.com/en/digital-asset-management-system) - v6.7 or higher. 
* Elvis administrator knowledge

# Installation

* Read the [instruction](https://helpcenter.woodwing.com/hc/en-us/articles/202965685-Plug-ins-introduction-management) on installing Elvis plug-ins.
* Copy the `api_plugin_sample` folder to the Elvis Server plug-ins folder: `<Elvis Config>/plugins/active`.
* Activate the plug-in through the Elvis Management Console.

# Usage

Assume your Elvis Server runs under http://localhost:8080, with the installation of this plugin, all requests to http://localhost:8080/plugins/api_plugin_sample/ are proxied to https://jsonplaceholder.typicode.com/

As an example, we're going to get the first post from the API. Calling the typicode API directly looks like this:

```bash
curl -X GET "https://jsonplaceholder.typicode.com/posts/1"
```

With response:
```json
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
}
```

Now let's use this through our API plugin:
```bash
curl -X GET "http://localhost:8080/plugins/api_plugin_sample/posts/1"
```

This call will actually fail with the following error message:
```json
{
  "errorname": "AuthenticationCredentialsNotFoundException",
  "message": "Not logged in",
  "errorcode": 401
}
```

Reason is that our Elvis API Plugin requires Elvis authentication (ROLE_USER). So we first have to authenticate and after that we can call the API.

Authenticate to Elvis request:
```bash
curl -X POST "http://localhost:8080/services/apilogin?username=admin&password=changemenow"
```

Authentication response:
```json
{
  "loginSuccess": true,
  "loginFaultMessage": null,
  "serverVersion": "6.7.0.0",
  "authToken": "eyJhbG ... 42SdgwQ"
}
```

We can authenticate our API call by passing the returned `authToken` in the Authorization: Bearer header:
```bash
curl -X GET -H "Authorization: Bearer eyJhbG ... 42SdgwQ" "http://localhost:8080/plugins/api_plugin_sample/posts/1"
```

Note: the actual authToken is a lot longer, it's trimmed in this example to keep it readable.