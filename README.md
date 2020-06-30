# Whack-a-Penguin

An iOS game app from HackingWithSwift.com projects that demonstrates the use of SKCropNode, SKTexture, GCD's asyncAfter(), and SKAction types.

<img src="https://github.com/igibliss00/Whack-a-Penguin/blob/master/README_assets/1.png" width="400">

<img src="https://github.com/igibliss00/Whack-a-Penguin/blob/master/README_assets/2.png" width="400">

## Features

### SKCropNode

SKCropNode is a subclass of SKNode that’s a container node to crop other nodes in the scene.  It’s used for hiding and revealing other node to create desirable effects.  Crop nodes use additional offscreen memory buffer which means they add notably more overhead to the app.  

```
let cropNode = SKCropNode()
cropNode.position = CGPoint(x: 0, y: 15)
cropNode.zPosition = 1
cropNode.maskNode = SKSpriteNode(imageNamed: "whackMask")

charNode = SKSpriteNode(imageNamed: "penguinGood")
charNode.position = CGPoint(x: 0, y: -90)
charNode.name = "character"
cropNode.addChild(charNode)

addChild(cropNode)
```

By adding a character node to the crop node, setting the mask node of the crop node to sprite node called “whackMask”, and then adding the crop node to the scene, you can create an effect where the mask node of the crop node is hiding the character node.  Everything with a colour is visible with crop nodes and everything transparent is invisible. 


### maskNode

When the crop node’s contents are rendered, the crop node first draws its mask into a private buffer. Then, it renders its children. When rendering its children, each pixel is verified against the corresponding pixel in the mask. If the pixel in the mask has an alpha value of less than 0.05, the image pixel is masked out. Any pixel not rendered by the mask node is automatically masked out.
The default value of this property is nil, which indicates that the child nodes should not be cropped.
([Source](https://developer.apple.com/documentation/spritekit/skcropnode/1520449-masknode))


### SKTexture

An SKTexture object is an image that can be applied to SKSpriteNode and SKShapeNode objects, particles created by an SKEmitterNode object, or tiles used in an SKTileMapNode. A texture object manages the texture data and graphics resources that are needed to render the image. Most texture objects are created from source images stored in your app bundle—your game’s artwork. Once created, a texture object’s contents are immutable. Multiple sprites can share the same texture object, sharing a single resource. 
([Source](https://developer.apple.com/documentation/spritekit/sktexture))

SKTexture is similar to what UIImage is to UIImageView.  It holds the image, but is not responsible for displaying it.  It’s computationally more efficient to use SKTexture to be assigned to a SKSpriteNode’s texture property, instead of creating brand new node every time. 

```
func show(hideTime: Double) {
    if isVisible { return }

    charNode.run(SKAction.moveBy(x: 0, y: 80, duration: 0.05))
    isVisible = true
    isHit = false

    if Int.random(in: 0...2) == 0 {
        charNode.texture = SKTexture(imageNamed: "penguinGood")
        charNode.name = "charFriend"
    } else {
        charNode.texture = SKTexture(imageNamed: "penguinEvil")
        charNode.name = "charEnemy"
    }
}
```

Here SKTexture is being used to flip flop between a good Penguin and an evil Penguin, instead of using different charNodes for each.

### asyncAfter(deadline:execute:)

This method is for executing a work item, passed to the parameter called “execute”, after a certain delay, passed to the parameter called “deadline”.  This can be used in the main thread or in the background thread.

```
DispatchQueue.main.asyncAfter(deadline: .now() + delay) { [weak self] in
    self?.createEnemy()
}
```

The method is used in the project in order to delay creating enemies so that the player can be ready. This same effect could alternatively be created with perform(_:with:afterDelay:).
