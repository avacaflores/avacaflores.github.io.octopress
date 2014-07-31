---
layout: post
title: "Removiendo SKnode en iOS 8"
date: 2014-07-30 21:12:02 -0400
comments: true
sharing: true
footer: true
categories: [Objective-C,SpriteKit]
---
La nueva versión de Xcode 6 trae muchos cambios entre ellos algunos que mas bien parecerían ser errores, tal es el caso de la función `removeFromParent` en SpriteKit. 

##El problema  

Normalmente en un juego desarrollado en SpriteKit se utiliza el siguiente código para eliminar todos los **SKNode** que estan fuera del **SKScene** o que cumplen determinada condición. El siguiente código es usado por ejemplo en la función `didSimulatePhysics`

```
[self enumerateChildNodesWithName:@"mySprite" usingBlock:^(SKNode *node, BOOL *stop) {
  
  [node removeFromParent];
  
}];
```

Sin embargo, este código que fue usado en varias versiones de iOS no es válido en iOS 8 ya que causa un error cuando es ejecutado. Aparentemente el usar `removeFromParenet` dentro de un bloque no es un procedimiento valido a partir de la versión de Xcode 6.

##La solución

Para lograr remover los **SKNode** de un **SKScene** sin utilizar este código tendremos de dilatar el comando hasta que todos los nodos sean enumerados, la propuesta es usar el siguiente código:

```
NSMutableArray* nodesToRemove = [NSMutableArray array];
  
[self enumerateChildNodesWithName:@"mySprite" usingBlock:^(SKNode *node, BOOL *stop) {

  [nodesToRemove addObject:node];

}];

for (SKNode* node in nodesToRemove){
  
  [node removeFromParent];
}
``` 
 
Simplemente guardamos todas las referencias de los nodos a eliminar en un **array** y luego removemos todos los nodos que fueron almacenados.

Esperemos que este **workaround** sea algo temporal y que este comportamiento sea solo un error que será solucionado en versiones futuras de Xcode. La versión en la cual fue utilizado este código es Xcode 6 (Beta 4).
