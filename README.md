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
``` js
import (
	"go.mongodb.org/mongo-driver/bson/primitive"
)

type ProhibitedItem_en struct {
	ID             primitive.ObjectID  `bson:"_id,omitempty" json:"_id,omitempty"`
	Destinasi      string              `bson:"Destinasi" json:"Destinasi"`
	BrangTerlarang string              `bson:"Barang Terlarang" json:"Barang Terlarang"`
}
```
NB: Kalian bisa mengganti nama type "prohibiteditem_en" nya sesuai dengan nama yang kalian inginkan.
