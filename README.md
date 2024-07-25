# swagger

Edit menggunakan [editor.swagger.io](https://editor.swagger.io/) dengan import URL :

https://raw.githubusercontent.com/posind/swagger/main/openapi.yaml

![image](https://github.com/jscroot/swagger/assets/11188109/d5cc9ae1-e73d-4eaa-929d-dab1f74fe2cd)

Jika sudah benar maka bisa langsung isinya di timpa di file openapi.yml di repo ini.

Pastikan semua endpoint terisi deskripsi jelas. Bisa set gembok authorize untuk user yang memiliki token dan pada request body sudah ada json example
![image](https://github.com/jscroot/swagger/assets/11188109/346d19b1-6294-4538-a287-d5d271dd2523)

NB : Jika tidak suka dengan YAML bisa diganti dengan JSON

# struct model for crud 

contoh model struct yang akan digunakan untuk mengakses collection di database MongoDB
package model

import (
	"go.mongodb.org/mongo-driver/bson/primitive"
)

type ProhibitedItem_en struct {
	ID             primitive.ObjectID  `bson:"_id,omitempty" json:"_id,omitempty"`
	Destinasi      string              `bson:"Destinasi" json:"Destinasi"`
	BrangTerlarang string              `bson:"Barang Terlarang" json:"Barang Terlarang"`
}

NB: Kalian bisa mengganti nama type "prohibiteditem_en" nya sesuai dengan nama yang kalian inginkan.

# endpoint login & register
ini adalah contoh endpoint untuk login dan register dengan golang 
``` js 
package controller

import (
	"context"
	"encoding/json"
	"net/http"

	"github.com/gocroot/config"
	"github.com/gocroot/model"
	"go.mongodb.org/mongo-driver/bson"
	"go.mongodb.org/mongo-driver/bson/primitive"
	"golang.org/x/crypto/bcrypt"
)

func Register(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodPost {
		http.Error(w, "Method not allowed", http.StatusMethodNotAllowed)
		return
	}
	var user model.User
	_ = json.NewDecoder(r.Body).Decode(&user)

	hashedPassword, err := bcrypt.GenerateFromPassword([]byte(user.Password), bcrypt.DefaultCost)
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}
	user.Password = string(hashedPassword)
	user.ID = primitive.NewObjectID()

	collection := config.Mongoconn.Collection("users")
	_, err = collection.InsertOne(context.Background(), user)
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

	w.WriteHeader(http.StatusCreated)
	json.NewEncoder(w).Encode(user)
}



func Login(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodPost {
		http.Error(w, "Method not allowed", http.StatusMethodNotAllowed)
		return
	}
	var loginRequest model.LoginRequest
	_ = json.NewDecoder(r.Body).Decode(&loginRequest)

	collection := config.Mongoconn.Collection("users")
	var user model.User
	err := collection.FindOne(context.Background(), bson.M{"email": loginRequest.Email}).Decode(&user)
	if err != nil {
		http.Error(w, "Invalid email or password", http.StatusUnauthorized)
		return
	}

	err = bcrypt.CompareHashAndPassword([]byte(user.Password), []byte(loginRequest.Password))
	if err != nil {
		http.Error(w, "Invalid email or password", http.StatusUnauthorized)
		return
	}

	w.WriteHeader(http.StatusOK)
	json.NewEncoder(w).Encode(user)
}
``` 
NB: jangan lupa untuk update case di route.go nya juga kalian bisa menyesuaikan dengan nama collection kalian di mongoDB masing-masing.
``` js 
case method == "POST" && helper.URLParam(path, "/webhook/endpoint_user/user"):
		controller.Login(w, r)
	case method == "POST" && helper.URLParam(path, "/webhook/endpoint_user/user"):
		controller.Register(w, r)
```        