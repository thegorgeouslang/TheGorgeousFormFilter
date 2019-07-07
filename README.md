# FormFilter

Rules and error messages for form input fields are created by adding values to the Rules and Message properties:

**project/controllers/helpers/FormHelper.go**
```Go
package helpers

import (
	. "github.com/thegorgeouslang/formfilter"
)

type formHelper struct {
	FormValidator
}

var FormHelper *formHelper

func init() {
	fh := formHelper{}
	fh.Rules = map[string]string{
		"email":    `\w{2,64}@\w{2,64}\.\w{2,64}(\.\w+)?`,
		"username": `\w+`,
		"password": `[A-Za-z\d@$!%*#?&]{8,}`,
	}

	fh.Messages = map[string]string{
		"email":    "Invalid email format",
		"username": "The username must contains only alphanumeric values",
		"password": `The password must contain at least 8 characters,
                    1 uppercase character [A-Z],
                    1 lowercase character [a-z],
                    1 digit [0-9],
                    1 special character (!, $, #, etc)`,
	}
	FormHelper = &fh
}

```
**project/controllers/AuthController.go**

```Go
package controllers

import (
	"net/http"
	. "project/controllers/helpers"
)


type authController struct{}

func AuthController() *authController {
	return &authController{}
}

func (this *authController) Signup(w http.ResponseWriter, r *http.Request) {
	// filtering form inputs
	if errs := FormHelper.Filter(r); len(errs) > 0 {
		http.Error(w, errs[0].Error(), http.StatusForbidden)
		return
	}

}
```
**main.go**
```Go
package main

import (
	"net/http"
	"project/controllers"
)

func main() {
	// curl -d "email=t@t.com" -X POST http://localhost:3000/signup
	http.HandleFunc("/signup", controllers.AuthController().Signup)

	panic(http.ListenAndServe(":3000", nil))
}
```

```
Invalid email format
```
**by [James Mallon]**

[James Mallon]: <https://www.linkedin.com/in/thiago-mallon/>
