Para escribir matemática formal como se pide en la FIUBA, el secreto está en dominar los **tres bloques básicos de la sintaxis**:

1. **Conjuntos por comprensión:** $\{\text{Elemento} \mid \text{Condición}\}$
    
2. **Cuantificadores lógicos:** $\exists$ (existe) y $\forall$ (para todo).
    
3. **Estructura de definición de funciones:** $f: A \to B$.
    

Vamos a desglosar cómo redactar estas expresiones paso a paso.

## 1. La estructura por comprensión

Cuando querés definir un conjunto no listando sus elementos uno por uno (extensión), sino mediante una regla, usás corchetes con esta estructura estándar:

$$\{ \underbrace{\text{variable} \in \text{Conjunto Universo}}_{\text{¿De dónde sale el elemento?}} \quad / \quad \underbrace{\text{Propiedad}(\text{variable})}_{\text{¿Qué condición debe cumplir?}} \}$$

- En la FIUBA es muy común usar la barra diagonal `/` o los dos puntos `:` para indicar el _"tal que"_. También se usa la barra vertical $\mid$.
    

## 2. Los cuantificadores y conectores esenciales

Para redactar demostraciones o definir conjuntos, estos son los símbolos que vas a usar constantemente:

| **Símbolo**                   | **Notación**            | **Significado**               | **Ejemplo formal**                                                                                   |
| ----------------------------- | ----------------------- | ----------------------------- | ---------------------------------------------------------------------------------------------------- |
| **Cuantificador existencial** | $\exists$               | Existe al menos un...         | $\exists \, x \in \mathbb{R} / x > 0$ _(Existe al menos un $x$ real tal que $x$ es mayor a 0)_       |
| **Existencia única**          | $\exists!$              | Existe un único...            | $\exists! \, x \in \mathbb{R} / x + 2 = 5$                                                           |
| **Cuantificador universal**   | $\forall$               | Para todo / Para cualquier... | $\forall \, x \in \mathbb{R}, x^2 \ge 0$ _(Para todo $x$ en los reales, $x^2$ es mayor o igual a 0)_ |
| **Pertenencia**               | $\in$ / $\notin$        | Pertenece / No pertenece      | $3 \in \mathbb{N}$                                                                                   |
| **Inclusión**                 | $\subset$ / $\subseteq$ | Subconjunto / Contenido       | $A \subset B$ _(El conjunto A está contenido en B)_                                                  |
| **Implicación**               | $\Rightarrow$           | Entonces / Implica            | $x = 2 \Rightarrow x^2 = 4$                                                                          |
| **Doble implicación**         | $\Leftrightarrow$       | Si y solo si (equivalencia)   | $x + 1 = 3 \Leftrightarrow x = 2$                                                                    |

## 3. Ejemplo práctico: Cómo construir una definición desde cero

Imaginá que querés escribir formalmente el conjunto de los **números pares**.

1. **Idea en español:** _"Son los números enteros que se pueden escribir como 2 multiplicado por algún otro entero"_.
    
2. **Identificación de variables:** Decimos que nuestro número es $x \in \mathbb{Z}$.
    
3. **Condición:** Existe algún $k \in \mathbb{Z}$ tal que $x = 2k$.
    
4. **Sintaxis formal armada:**
    

$$\text{Pares} = \{ x \in \mathbb{Z} / \exists \, k \in \mathbb{Z} / x = 2k \}$$

## 4. Estructura formal para definir Funciones

Antes de escribir la imagen o el dominio, siempre tenés que presentar la función con su dominio y codominio:

$$f: A \to B \quad / \quad f(x) = \text{fórmula}$$

- **$A$:** Dominio (de dónde salen las $x$).
    
- **$B$:** Codominio (donde caen las $y$).
    
- **$\to$:** Va de $A$ hacia $B$.
    

Con esta base, podés definir por ejemplo el **Gráfico de una función** ($\text{Graf}(f)$):

$$\text{Graf}(f) = \{ (x, y) \in A \times B / y = f(x) \}$$

_(Se lee: El conjunto de pares ordenados $(x,y)$ pertenecientes al producto cartesiano $A \times B$ tales que $y$ es igual a $f(x)$)._