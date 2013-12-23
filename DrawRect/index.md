---
layout: page
title: DrawRect
---

The     -drawRect: method is where all drawing should take place in an NSView subclass. An example would look like this:

    
- (void)drawRect:(NSRect)rect
{
    [[NSColor whiteColor] set];
    NSRectFill([self bounds]);
    
    [[NSColor greenColor] set];
    NSRectFill(NSInsetRect([self bounds], 5, 5));
}


Note how the     rect parameter is ignored.

You may be wondering, what is the parameter for? It tells you what areas need to be redrawn. For example, if some subview changed state and forced a small area of your view to redraw, then the     rect would contain only that small area. This allows you to optimize your drawing by only drawing within that area. Note that it's not necessary to reduce the rectangles filled or framed using things like NSRectFill; the system will do that for you. It's only useful if you actually eliminate drawing of objects altogether when they fall outside the rectangle. In the spirit of OptimizeLater, you should ignore     rect unless it's absolutely necessary.

A CommonProblem is using the     rect to determine where to draw. *This is wrong.* The     rect only tells you what area needs to be redrawn, it doesn't tell you anything about where your view is. If you need to find the dimensions of your view, use     [self bounds]. Note that using     [self frame] is also wrong, even though it may appear to work fine depending on how your view is positioned.
 Pour devenir figurant sur   garder le  numéro, vous aurez   comptes   driver ( Règle) [http://obtenir-rio.info rio bouygues]. Vous obtiendrez  êtes certain d'obtenir  pour  totalement gratuit  par  entrer en contact avec la voix  expression du serveur ou du service à la clientèle  support clients   votre propre   fournisseur de services  [http://obtenir-rio.info/rio-bouygues rio bouygues] . Vous ne  CAN   get un SMS  avec vos . Avec votre  [http://obtenir-rio.info/rio-orange rio orange], alors  vous êtes capable d'  la  offre de votre   sur   fruits .

