# S.O.L.I.D.
## Single responsibility principle
A class should have only one reason to change.

**Wrong**

```
function Circle(radius) {
	this.radius = radius;
}

function Square(length) {
	this.length = length;
}

function AreaCalculator(shapes) {
	this.shapes = shapes;
}

AreaCalculator.prototype.sum = function() {
	// some sum function
	// returns numbers sum of areas of all provided shapes
}

// THIS METHOD DOESN'T BELONG HERE!!!
AreaCalculator.prototype.output = function() {
	returns `The sum of all shapes is ${this.sum()}.`;
}
```
**Correct**
```
function CalculatorOutput(areas) {
	this.areas = areas;
}

CalculatorOutput.prototype.polite = function() {
	return `I do belive the sum of provided areas is ${this.areas.sum()}`.;
}

CalculatorOutput.prototype.rude = function() {
	return `Pshh, whatev, I guess the sum might be ${this.areas.sum()}`.;
}
```
**Main**
```
const circle = new Circle();
const square = new Square();
const shapesAreas = [circle, square];

const areas = new AreaCalculator(shapesAreas);
const output = new CalculatorOutput(areas);

// returns area calc results in multiple formats
output.polite();
output.rude();
// can now add new features easily without altering AreaCalculator!!!
```
## Open/Closed principle
You should be able to extend a class without modifying it.

**Wrong**
```
AreaCalculator.prototype.sum = function() {
	const areasArray = this.shapes.map(function(shape){
		if (shape instanceof Square) {
			return Math.pow(shape.length, 2);
		} else if (shape instanceof Circle) {
			return Math.PI * Math.pow(shape.radius, 2);
		}
	});
	return areasArray.reduce(function(acc, cur){
		return acc + cur;
	});
}
```
**Correct**
```
Square.prototype.area = function() {
	return Math.pow(this.length, 2);
}
Circle.prototype.sum = function() {
	return Math.PI * Math.pow(this.radius, 2);
}
AreaCalculator.prototype.sum = function() {
	const areasArray = this.shapes.map(function(shape){
		return shape.area();
	});
	return areasArray.reduce(function(acc, cur){
		return acc + cur;
	});
}
```

## Liskov substitution principle
Derived classes must be substitutable for their base classes.

**Wrong**
```

function Rectangle(width, height) {
	this.width = width;
	this.height = height;
}

Rectangle.prototype.area = function() {
	return this.width * this.height;
}

// UH OH, you are changing how this 'rectangle' works!
function Square(height) {
	Rectangle.call(this, height, height);
}

Square.prototype = Object.create(Rectangle.prototype);

// now if you try to make new 'subclass' of Rectangle from a Square
// the behavior will be conterintuitive

function AwesomeRectangle(width, height) {
	Square.call(width, height);
	this.awesome = true;
}

AwesomeRectangle.prototype = Object.create(Square.prototype);

const awesomeRectangle - new AwesomeRectangle(4, 5);
awesomeRectangle.area(); // RETURNS 16 instead of 20!!!
// this not working means it violats LSP.
```
## Interface segregation principle
Make fine-grained interfaces with specific methods.

**Wrong**

```
function Shape() {}
Shape.prototype.area = function() {}
Shape.prototype.volume = function() {}
// CANNOT ASSUME THAT SHAPE IS 3-D!!!

function Triangle(base, height) {
	this.base = base;
	this.height = height;
}
Triangle.prototype = Object.create(Shape.prototype);
// Now triangle has an unnnecessary volume method.
```

**Correct**

```
// Better to make two separate classes
function Shape() {}
Shape.prototype.area = function(){}
function SolidShape() {}
SolidShape.prototype.volume = function(){}

const cube = new SolidShape();
```
## Dependency inversion principle

Depend on abstractions, not on concretions.

```
// If you're making a new instance of something inside another class,
// dependency is happening.
function Picture(color, bg) {
	this.shape = new Shape();
	this.shape.setColor(color);
	this.background = bg;
}

// Now works for all instances of Shape and isn't coupled to outside changes.
function Picture(shape, color, bg) {
	this.shape = shape;
	this.shape.setColor(color);
	this.background = bg;
}
const triangle = new Triangle(5, 9);
const picture = new Picture(triangle, 'blue', 'green');
```