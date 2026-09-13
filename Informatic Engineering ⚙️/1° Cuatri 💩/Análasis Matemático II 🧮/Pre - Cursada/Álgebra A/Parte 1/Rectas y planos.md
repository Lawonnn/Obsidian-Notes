# Rectas
Son espacios lineales sencillos

### ¿Cómo se describe una recta?
$$
\{(x,y) \in R^2: y=mx+b\}
$$
Siendo:
$m$ = la pendiente
$b$ = la ordenada

### Recta vectorial
Se necesita
- Un vector $\overrightarrow{v}$ que indique la dirección de la recta.
- Un vector $\overrightarrow{w}$ que traslade esa recta hasta un punto por el cual queremos que pase
$$
L = \{X \in R^n : X = t\overrightarrow{v} + \overrightarrow{w}, t \in R \}
$$

### ¿Cuando un punto pertenece a una recta?
Recta: $L = \{ X \in R^2: X = t(2,1) + (5-1)\}$
Punto: $(3,-2)$

$$
X = t(2,1) + (5-1)
$$
$$
X = (2t,t) + (5-1)
$$
$$
X = ((2t+5),(t-1))
$$
$$
X = \begin{cases} x = 2t+5 \\ y = t-1 \end{cases}
$$
$$
X = \begin{cases} 3 = 2t+5 \\ -2 = t-1 \end{cases}
$$
$$
X = \begin{cases} 3 = 3\\ -1 = t \end{cases} \implies P(3,-2) \in L
$$
### ¿Cuando dos rectas son paralelas
- Cuando sus vectores directores son múltiplos

### ¿Cuando dos rectas son perpendiculares?
- Cuando sus vectores directores son ortogonales

### Ecuación implícita
Es una relación $ax + by = c$

### Ecuación vectorial de la implícita
Recta: $L = \{(x,y) \in R^2: x+2y=-1 \}$

$$
x = -1 + 2y \implies L = \begin{cases} x =-1 -2y \\ y = y\end{cases}
$$
$$
L = (-1-2y,y)
$$
$$
L = (-2y,y) + (-1,0)
$$
$$
L = y(-2,1) + (-1,0)
$$

# Planos
Una recta "contiene una dirección", mientras que el plano contiene infinitas

### Ecuación vectorial
$$
L:t\overrightarrow{v}+s\overrightarrow{w}+\overrightarrow{u}
$$
$$
\Pi = {X \in R^3: X = t\overrightarrow{v}+s\overrightarrow{w}+\overrightarrow{u},t,s \in R}
$$

### Ecuación normal
$$
\Pi = \{X \in R^2: X = \overrightarrow{N} \cdot (\overrightarrow{X} - \overrightarrow{P})\}
$$
Siendo:
- $\overrightarrow{P}$ : el punto por el que pasa el plano
- $\overrightarrow{N}$: la normal del plano

### ¿Cuando son dos planos paralelos?
Cuando sus vectores normales son múltiplos uno del otro

### ¿Cuando un plano es perpendicular a una recta?
Cuando el vector normal del plano es múltiplo del vector directo de la recta

### Producto vectorial
Si $\overrightarrow{v},\overrightarrow{w} \in R^3$ y no son paralelos, entonces definiremos $\overrightarrow{u} \in R^3$, llamado *el producto vectorial entre $\overrightarrow{v}$ y $\overrightarrow{w}$*, cuya propiedad principal es que es perpendicular tanto a $\overrightarrow{v}$ como a $\overrightarrow{w}$
$$
\overrightarrow{u} = \overrightarrow{v} \times \overrightarrow{w}
$$

## Intersección de subespacios de $R^3$
### Intersección de planos
Existen 3 posibles resultados al buscar una intersección
- Se "atraviesan" a lo largo de un recta
- Son paralelos
- Son el mismo plano

Ejemplo 1:
$\Pi = \{(x,y,z) \in R^3: 2x-3y+2z=2\}$ 
$\Pi' = \{(x,y,z) \in R^3: x-2y-z=0\}$ 

$$
\begin{cases} 2x-3y+2z=2 \\ x-2y-z=0 \end{cases}
$$

$$
x = 2y+z
$$

$$
 2(2y+z)-3y+2z=2
$$

$$
y+4z=2 \implies y = 2-4z
$$

$$
x=2(2-4z)+z \implies x=4-7z
$$

$$
\begin{cases} x=4-7z \\ y=2-4z \\ z=z \end{cases} \implies (4-7z,2-4z,z)
$$
$$
(-7z,-4z,z)+(4,2,0)
$$
$$
z(-7,-4,1)+(4,2,0)
$$
