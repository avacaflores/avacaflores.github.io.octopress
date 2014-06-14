---
layout: post
title: "plist en Swift"
date: 2014-06-12 07:12:02 -0400
comments: true
sharing: true
footer: true
categories: [Swift]
---
La presentación del nuevo leguaje de programación Swift a despertado a un grupo muy grande programadores y no programadores, todos ansiosos por experimentar con el nuevo juguete.

La primera idea que uno tiene es que al ser un leguaje nuevo debemos limitarnos solamente a sus comandos, funciones, etc, sin embargo en la práctica eso no es posible. Hay una infinidad de librerías en Cocoa que usan clases de Objective-C que son fundamentales para la programación en iOS y OS X, tal es el caso de las librerías necesarias para leer un plist.

El siguiente ejemplo muestra la manera mas elegante que encontré para realizar la tarea de leer un plist el cual esta compuesto por un arreglo de diccionarios.

##El plist
{% img /images/blog/plist.png %}    
El plist contiene una estructura básica de un `array` y n `dictionary`

##Abrir el plist
Primeramente necesitamos definir la ruta y posteriormente gargar el archivo en memória
```
let path = NSBundle.mainBundle().pathForResource("List", ofType: "plist")
let array = NSArray(contentsOfFile: path)
```
A partir de esto podemos consultar el contenido del `array` usando `println(array)` y obtendremos el resultado en la consola

    (
            {
            Nombre = Hugo;
            Telefono = 2123268000;
        },
            {
            Nombre = Esteban;
            Telefono = 2125648765;
        },
            {
            Nombre = Sergio;
            Telefono = 2127547654;
        }
    )

Hasta ahora todo parece muy facil e intunitivo, sin embargo obtener los valores de cada diccionario individualmente es la parte que necesita de intrucciones especiales

##Iterar el array

Para realizar esta tarea es necesario realizar el **casting** a `AnyObject` y luego hacer un nuevo **casting** de cada elemento a `NSDictionay` de la siguiente manera:
```
for user:AnyObject in array {
  
  let dict:NSDictionary = user as NSDictionary
  
  println(dict["Nombre"])
  println(dict["Telefono"])				
} 
```
A partir de aquí podremos obtener los valores que estan contenidos en cada diccionario dentro del plist.