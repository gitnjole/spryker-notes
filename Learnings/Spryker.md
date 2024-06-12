## @api keyword

Adding @api in the doc block describes that the method is available outside the module.

## @method on top of class

The Spryker helper methods inside the abstraction class will find the factory, but your IDE does not know the proper type of the factory. 
This is why we add the @method annotation on top of the class. The code will work without it, but it improves usability inside the IDE.
