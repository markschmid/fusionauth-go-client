## FusionAuth Go Client ![semver 2.0.0 compliant](http://img.shields.io/badge/semver-2.0.0-brightgreen.svg?style=flat-square)


Use this client to access the FusionAuth APIs in your Go application. For additional information and documentation on FusionAuth refer to [https://fusionauth.io](https://fusionauth.io).

## Credits
- [@medhir](https://github.com/medhir) Thank you for the initial commit and initial implementation of the Go client!
- The FusionAuth team - couldn't have done it without you!

## Installation

```
go get github.com/FusionAuth/fusionauth-go-client/pkg/fusionauth
```

## Example Usage

The following example uses the FusionAuth Go client to create a request handling function that logs in a user: 
```go
package example

import (
	"encoding/json"
	"net/http"
	"net/url"
	"time"

	client "github.com/FusionAuth/fusionauth-go-client/pkg/fusionauth"
)

const host = "http://localhost:9011"

var apiKey = "YOUR_FUSIONAUTH_API_KEY"
var httpClient = &http.Client{
	Timeout: time.Second * 10}

var baseURL, _ = url.Parse(host)

// Construct a new FusionAuth Client
var auth = &client.FusionAuthClient{
	BaseURL:    baseURL,
	APIKey:     apiKey,
	HTTPClient: httpClient}

// Credentials describes the JSON request for a user login
type Credentials struct {
	LoginID  string `json:"loginId"`
	Password string `json:"password"`
}

// Login logs in the user using the FusionAuth Go client library
func Login() http.HandlerFunc {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		// Read response body
		var credentials Credentials
		defer r.Body.Close()
		json.NewDecoder(r.Body).Decode(&credentials)
		// Use FusionAuth Go client to log in the user
		authResponse, err := auth.Login(credentials)
		if err != nil {
			http.Error(w, err.Error(), http.StatusBadRequest)
			return
		}
		// Write the response from the FusionAuth client as JSON
		responseJSON, err := json.Marshal(authResponse)
		if err != nil {
			http.Error(w, err.Error(), http.StatusInternalServerError)
			return
		}
		w.Header().Set("Content-Type", "application/json")
		w.WriteHeader(http.StatusOK)
		w.Write(responseJSON)
	})
}
```

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/FusionAuth/fusionauth-go-client.

If you find an issue with syntax, etc - this is likely a bug in the template. Feel free to submit a PR against the Client Builder project.
https://github.com/FusionAuth/fusionauth-client-builder
https://github.com/FusionAuth/fusionauth-client-builder/blob/master/src/main/client/go.client.ftl


## License

The code is available as open source under the terms of the [Apache v2.0 License](https://opensource.org/licenses/Apache-2.0).
