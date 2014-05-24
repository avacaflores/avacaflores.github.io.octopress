---
layout: post
title: "Scopes in Rails 4"
date: 2014-05-23 00:12:02 -0400
comments: true
sharing: true
footer: true
categories: [Rails]
---
Rails es un framework muy flexible, existen muchas tareas que se las pueden hacer de diferentes maneras, sin embargo esta flexibilidad se ve empañada por la confusión que existe en internet. Cada version de Rails incorpora nuevos comandos y retira otros, también se puede ver que la sintaxis de los comandos ha ido cambiando en cada versión.

En este post me quiero referir específicamente a los **scopes** y su sintaxis en Rails 4, describiré la sintaxis que me parece mas clara y sencilla. 

Primeramente definamos los modelos de ejemplo

``` ruby
class Product < ActiveRecord::Base
  belongs_to :type
end

class Type < ActiveRecord::Base
  has_many_ :products
end
```

Así mismo, asumamos que cada modelo cuenta con un campo `title`

##scope estático

Serán aquellos que actúan sobre un modelo y que tienen una condición fija, bajo estas premisas, estos podrían ser scopes definidos en los modelo `Product`:

``` ruby
scope :fixedtitle, lambda { where(:title => 'computer') }
```
Podemos llamar el scope con el siguiente comando `Product.fixedtitle` que producira el seguinete resultado:

    SELECT "products".* 
    FROM "products"  
    WHERE "products"."title" = 'computer'

Otro ejemplo de scope estático podría ser:
``` ruby
scope :titlecontains, lambda { where("title LIKE '%p%'") }
```
Que lo llamariamos con el siguiente comando `Product.titlecontains` y producira el siguiente resultado:

    SELECT "products".* 
    FROM "products"  
    WHERE (title LIKE '%p%')

Incluso podríamos usar ambos scopes en la misma consulta `Product.fixedtitle.titlecontains`

    SELECT "products".* 
    FROM "products"  
    WHERE "products"."title" = 'computer' AND (title LIKE '%a%')

##scope variable

En este caso lo que esperamos es que el scope reciba una variable dinámicamente, para lograrlo debemos usar `|variable|` para recibir el valor:

``` ruby
scope :variabletitle, lambda { |term| where("title LIKE '%#{term}%'") }
```
Deberemos pasar el valor en la llamada al scope de la siguiente manera `Product.variabletitle('compu')` que producira el siguiente resultado:

    SELECT "products".* 
    FROM "products"  
    WHERE (title LIKE '%compu%')

##scope variable con dos modelos

Por ultimo, el caso mas complejo que me tomo varias horas de investigación y pruebas y que me empujo a escribir este post. Como podrán imaginar necesitamos usaremos un scope variable y también usaremos **joins** para enlazar al segundo modelo

``` ruby
scope :bytype, lambda { |type_name| joins(:type).where(‘type.title’ => type_name) }
``` 
Llamaremos a este scope con `Product.bytype('electronics')` y producira el siguiente resultado:

    SELECT "products".* 
    FROM "products" INNER JOIN "types" ON "types"."id" = "products"."type_id" 
    WHERE "types"."title" = 'electronics'

**Scopes** en Rails es un tema bastante amplio sobre el cual hay mucho material (el cual no necesariamente es vigente ni). Esta propuesta es sumamente simple pero 100% funcional y puede ser extendida siguiendo el mismo patron.
