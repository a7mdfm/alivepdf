# Introduction #

I've been using the AlivePDF library for a few days now and have some observations that might help others just starting off.  I invite others to post what you've found here as well.

# Current Position #

There's two ways to set the current position.  You can use pdf.moveTo for drawing API operation and pdf.setXY for text based operations.

There are also pdf.setX and pdf.setY methods, but these have side effects, take a look at the code before using them.

# Adding Text #

Adding text is one of the most common things one would like to do while making a PDF.  There are a few ways of doing this.

## addText() ##

The addText() method will put out text at a given coordinate.  No word wrapping is done.

```
pdf.setFont( FontFamily.ARIAL , "", 32);
pdf.addText(Project.instance.title,30,50);
```

## writeText() ##

The writeText method will output text at the current location.  You can also specify a link that clicking on the text will take you to.

```
pdf.setFont( FontFamily.ARIAL , "", 32);
pdf.setXY( 20, 20);
pdf.writeText ( 32, "This is my text");
```

## addMultiCell() ##

addMultiCell ( pWidth:Number, pHeight:Number, pText:String, pBorder:**=0, pAlign:String='J', pFill:int=0)**

The addMultiCell lets you constrain text to a "cell" with a certain width.  The cell starts at the current position and is the given width.  The height IS NOT the height of the cell, it's the height of each line of text.

```
pdf.setFont( FontFamily.ARIAL , "", 32);
pdf.setXY( 20, 20);
pdf.addMultiCell ( 200, 32, "This is my text");
```

## Example ##

The following code:

```
pdf.setFont(FontFamily.ARIAL , "", 12);
			
pdf.addText("This is some addText() text",20,20 );
			
pdf.setXY( 100, 120);					
pdf.writeText(20,"The following lines are made using writeText().  This is my first statement.  You can use \\n to line break like this:\nThis is my first statement.  This is my first statement.  This is my first statement.  This is my first statement.  This is my first statement.  "); 			
			
pdf.setXY( 120, 400 );
pdf.addMultiCell( 200, 20,"This text added with addMultiCell() and is constrained within a cell.  It will word wrap, but won't go outside the bounding box.  It starts at the current position.");			
```

will produce the following pdf:

[![](http://lh6.google.com/marc.hughes/RtgPj8RO8xI/AAAAAAAABnA/SsvOw3EWeTw/s144/examplePdf.jpg)](http://picasaweb.google.com/marc.hughes/MarcSMusings/photo?authkey=_A3wwBLXd1A#5104847287772705554)





# Saving to a file in an AIR application #
```
public class ProjectPDFExporter
{
private var filename:String = “test.pdf”;
private var pdf:PDF;

 public function exportPdf(filename:String) : void
 {
  this.filename = filename;

  pdf = new PDF();

  // OLD WAY: pdf.addEventListener(Event.COMPLETE, onComplete);

  pdf.setDisplayMode (Display.FULL_PAGE,
  Layout.SINGLE_PAGE);

  pdf.addPage();
  pdf.setFont( FontFamily.ARIAL );
  pdf.addText(”My Teax”,1,10);
  pdf.setFont( FontFamily.ARIAL , “”, 32);
  pdf.addText(”Some more text”,10,30);

   savePDF();
 }

 protected function savePDF()
 {
  var f:FileStream = new FileStream();
  var file:File =
  File.applicationStorageDirectory.resolve( filename );
  f.open( file, FileMode.WRITE);
  var bytes:ByteArray = pdf.savePDF(Method.LOCAL);
  f.writeBytes(bytes);
  f.close();
 }
}
```

## Opening the PDF from AIR ##

This isn't strictly an AlivePDF specific thing, but I bet there's a lot of people who would like to open the PDF for the user once it's written out.  This will cause the user's browser to attempt to open the file.  If no browser is running, one should be opened.

```
var request:URLRequest = new URLRequest( file.url );
navigateToURL(request);	
```

You can also use AIR's HTML control to view PDF's

```
var request:URLRequest = new URLRequest( file.url );
var window:Window = new Window();
var html:HTML = new HTML();
			
if( HTMLControl.pdfCapability != 0 )
{
 // We can't display PDF in the app, lets hope their browser can.  If not, 
 // the browser will hopefully do a sane "Download to..." type thing.
 navigateToURL( request );
 return;
 } 

html.location = file.url;			
html.percentHeight=100;
html.percentWidth=100;			
window.addChild(html);
window.title = "PDF Export";
window.width = 800;
window.height = 650;
window.open();

```