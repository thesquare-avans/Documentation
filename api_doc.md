API spec
========

# Response format

All API requests will follow the same format, and data depending on the endpoint. Each request will use the format:

```json
{
	"success": true,
	"data": {
		// data here
	}
}
```

Requests that fail (errors, 404) will have `"success": false` and contain an error object.

```json
{
	"success": false,
	"error": {
		"code": "notFound",
		"message": "This resource can not be found"
	}
}
```

| Method | Endpoint | Parameters | Explanation |
| ------ | -------- | ---------- | ----------- |
| `GET` | `/users/:userid` | `:userid` as UUID | Get all info of current user |
| `POST` | `/users` | ```{<br>  "email": "john@example.org",<br>  "password": "secret",<br>  "name": "John Doe",<br>  "country": "US" // (2-digit code)<br>}``` | Create new user |
| `GET` | `/streams` | | Get (filtered) streams |