# HTML: The structure
HTML surge to share scientist documents
- It is the skeleton of a web page
- It's a mark language to structure and show the content of a browser
- It's based on tags

## Tags
```html
<p>Hello World</p>
```
- Marks that 
- The most of them have to be open and closed
- Some tags doesn't need to be close: `<img>`, `<br>`, `<hr>`
## Attributes
``` html
<a href="https://gooogle.com">UBA</a>
```

```html
<img src="img/cat.jpg" alt="A orange cat" >
```
- Extra information that is inside of a tag(when u open it): `attribute="value"`

## Basic Structure
```html
<!DOCTYPE html>
<html>
	<head> ---> Metadata Information
		<title>My first page</title>
	</head>
	<body>
		<h1>Hi FIUBA</h1>
		<p>This is a paragrahg</p>
	</body>
</html>
```

## Identifiers and classes
```html
<h1 id="core-title">Intro Camejo</h1>
<p class="destacado">HTML</p>
<p class="destacado">CSS</p>
```
- Both are attributes
- `id` identify uniquely a tag inside the document
- `class` group tags of similar tags

## Hierarchy: DOM tree
![[Pasted image 20261013195222.png]]

## Box Model
![[Pasted image 20261013211107.png]]
# CSS: The design
Organize the visual chaos of a primitive web
- CSS: Cascading Style Sheets
- Separate the structure (HTML) to style (CSS)

## Anatomy
```css
p {
	color: blue;
	font-size: 16px;
}
```
- **Selector** (`p`)
- **Propiedad** (`color`)
- **Valor** (`blue`)
### Selectors:
```css
p {color: red;} /* per element type*/
#mi-elemento {color: green;} /*per id*/
.mi-clase {color:blue;} /*per class*/
```
Here work the term **Specificity**![[Pasted image 20261013204201.png]]

# CSS to HTML
We have 3 mode to add CSS to our HTML
- Inline: add the style directly to the HTML. This one is unused
- Intern Style Sheet: in `<style>` we put the CSS like:
```html
...
	<head>
	...
	<style>
		p {
			color: blue;
			font-size: 16px;
		}
	</style>
	...
	</head>
...
```
- Extern Style Sheet: a file linked to our HTML:
```html
...
	<head>
		<link rel="stylesheet" href="style.css">
	</head>
...
```