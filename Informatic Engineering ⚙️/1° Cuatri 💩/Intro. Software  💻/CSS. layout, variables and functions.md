# Flexbox
Help us to order a list of thing in a row
```css
.menu {
	display: flex;
}
```

## Father
![[Pasted image 20261020191952.png]]

![[Pasted image 20261020192233.png]]

![[Pasted image 20261020192337.png]]

## Sons
```css
.seacher { flex-grow: 1} /* take the space that is left*/
.logo { flex-shrink: 0; } /* never get small */
.destacade { aling-self: center; } /* its own align-items*/
```

# Grid
## display: grid
```css
.grilla {
  display: grid;
  grid-template-columns: 200px 200px 200px;
}
```

![[Pasted image 20261020193242.png]]

## repeat()
```css
.galeria {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}
```
![[Pasted image 20261020193442.png]]

# Flexbox vs. Grid
### Flexbox
- **Una dimensión:** fila **o** columna
- El **contenido** define el tamaño de cada ítem
- Ideal para componentes: navbars, botones, centrar cosas

### Grid
- **Dos dimensiones:** filas **y** columnas
- El **contenedor** define la estructura
- Ideal para el layout de la página, galerías, dashboards

# Variables
```css
:root { --color-principal: #ff5a1f; }
.boton {
  background-color: var(--color-principal);
}
```

Example:
```css
:root   { --fondo: white;   --texto: #222222; }
.oscuro { --fondo: #121212; --texto: #eeeeee; }
body    { background-color: var(--fondo); color: var(--texto); }
```