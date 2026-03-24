<!--
author:   Marco Hamann

email:    marco.hamann@htw-dresden.de

version:  0.0.1

language: de

comment:  Dieser Kurs richtet sich an Studierende der Hochschule für Technik und Wirtschaft Dresden im Masterstudiengang Angewandte Robotik im 2. Semester.

import: https://raw.githubusercontent.com/LiaScript/CodeRunner/master/README.md

persistent: true

-->

# Räumliche Kinematik

## Einstieg

### Motivation

---

Beschreibung von **Positionen** im dreidimensionalen Raum, z. B.:

* Wo ist das Werkstück?
* Wie berechnet ein industrieller Roboterarm die Position eines Teils, das er aufnehmen soll, beispielsweise für die präzise Montage, das Schweißen oder das Verpacken?
* Wie bestimmt ein kollaborierender Roboter seine Position relativ zu menschlichen Arbeitskollegen, beispielsweise in der Mensch-Maschine-Kollaboration?
* Mathematisch: relativ zu welchem Koordinatensystem? .. relativ zum Basiskoordinatensystem des Endeffektor bzw. zum Weltkoordinatensystem?

---

Beschreibung von **Orientierungen** im dreidimensionalen Raum

* In welche Richtung kann ein Werkstück von einer Unterlage aufgenommen bzw. auf diese abgelegt werden?
* Wie ermittelt ein autonomes Fahrzeug seine Fahrtrichtung, um sicher durch den Verkehr zu navigieren?
* Wie bestimmt ein Roboterarm die Ausrichtung seiner Greifhand, beispielsweise für eine präzise Montage oder den Zusammenbau von Teilen?
* Wie passt ein Coboter seine Ausrichtung an, um sicher mit menschlichen Arbeitskollegen zusammenzuarbeiten?
* Mathematisch: Ausrichtung zwischen Koordinatensystemen

---

<div style="width: 100%; max-width: 1335px; margin: 0 auto; padding: 0; box-sizing: border-box; display: block;">
  <div style="position: relative; width: 100%; height: 0; padding-bottom: 51.78%; /* 479 / 925 ≈ 51.78% */">
    <img
      src="img/Position_Orientation.png"
      alt="Roboter-Greifer mit Beschriftung von Position und Orientierung"
      style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; border: 1px solid #ccc; border-radius: 8px; display: block; object-fit: contain;"
      loading="lazy"
    />
  </div>
</div>

<p>
  <strong>Abbildung:</strong> Technische Darstellung eines Endeffektors (Greifer), der ein Werkstück von oben greift. Der Begriff 'Position' ist am Anschlusspunkt am Manipulatorarm markiert. Die 'Orientierung' des Greifers ist durch die Griffrichtung "von oben" dargestellt.
</p>

---

<!-- style="color: purple;"-->
Wir benötigen ein ein **mathematisches Verfahren**, um Positionen (Translationen) und Orientierungen (Rotationen) zu beschreiben.

---

### Was ist Kinematik?

---

* **geometrische Beschreibung** von Bewegungen in (drei) Dimensionen. Dazu gehören: Positionen, Orientierungen und Transformationen in räumlichen Koordinatensystemen.
* in der Robotik: mathematische Methoden zur **Modellierung und Analyse der Bewegungen** von Robotern, insbesondere Starrkörpern. 
* zentrale Aspekte umfassen:
  - Kinematische Ketten: modellhafte Darstellung von Gelenken und Gliedern eines Roboters
  - Freiheitsgrade (DoF): Anzahl der unabhängigen Parameter, die zur Beschreibung der Lage eines Roboters erforderlich sind
  - Transformationen: mathematische Beschreibung von Positionen und Orientierungen durch Rotationsmatrizen, homogene Koordinaten, Quaternionen und duale Quaternionen
  - Bewegungsplanung: Berechnung von Bahnkurven für Roboterarme oder mobilen Roboter, um Hindernisse zu vermeiden und Ziele zu erreichen

---

<!-- style="color: purple;"-->
Steuerung und Navigation von Robotern in **Anwendungen** wie industrieller Automatisierung, medizinischer Robotik, autonomer Fahrzeugtechnologie und humanoider Robotik

---

### Schreibweisen

---

Die in dieser Vorlesung verwendeten Schreib- und Bezeichnungsweisen sind an ingenieurwissenschaftlichen Anwendungen angelehnt. Für die verwendeten Symole wird wie folgt vereinbart.

* **Skalare Größen** werden in der Regel mit kleinen lateinischen Buchstaben bezeichnet, zum Beispiel:
  - $t\in[0,\infty)$ für die Zeit
  - $u_j\in D_j$ mit $D_j\subseteq\mathbb{R}$ für den $j$-ten Gelenkparameter
  Ausnahme sind beispielsweise Winkel $\varphi\in[0,\infty)$, Winkelgeschwindigkeit $\omega$ (Einheit $1/s$) und Winkelbeschleunigung $\alpha$ (Einheit $1/s^2$)
* **Vektorielle Größen** werden in der Regel als lateinische Kleinbuchstaben mit einem Unterstrich versehen, zum Beispiel:
  - $\underline{a}\in\mathbb{R}^3$ für die Beschleunigung
  - Punktbezeichner werden ebenso aus diesem Symbolvorrat gewählt werden.
  Ausnahmen bestehen beispielsweise bei $\underline{\omega}_j\in\mathbb{R}^3$ für die Winkelgeschwindigkeit des $j$-ten Drehgelenks oder $\utilde{Q}\in\mathbb{R}^4$ für eine Quaternion.
* **Matrizen** beschreiben oft Koordinatenwechsel oder Drehungen und werden mit lateinischen Großbuchstaben bezeichnet, zum Beispiel:
  - $A\in\mathbb{R}^{3,3}$ 
* **Transformationen** werden mit großen grieschischen Buchstaben bezeichnet, zum Beispiel $$ \Phi:\mathbb{R}^3\to\mathbb{R}^3 $$

Besteht die Gefahr von Missdeutungen, wird die getroffene Bezeichnung explizit erläutert.

---

## Transformationen

### Euklidischer Raum

---

Für die mathematische Modellbildung wird der [euklidische dreidimensionale Raum](https://de.wikipedia.org/wiki/Euklidischer_Raum) benutzt, d. i. der affine dreidimensionale (Punkt-) Raum über dem euklidischen Vektorraum $\mathbb{R}^3$, ausgestattet mit dem Standardskalarprodukt $$
        \underline{a}\cdot\underline{b}=\begin{pmatrix} a_1 \\ a_2 \\ a_3\end{pmatrix}\cdot\begin{pmatrix} b_1 \\ b_2 \\ b_3\end{pmatrix}=\sum_{k=1}^3{(a_k\cdot b_k)} $$
**Längen- und Winkelmessung** lassen sich unter Benutzung des Skalarproduktes erklären, insbesondere die **Orthogonalität** von Vektoren bzw. affinen Unterräumen.

---

Lagen von Punkten, Geschwindigkeiten, Beschleunigungen beziehen sich grundsätzlich auf [kartesische Koordinatensysteme](https://de.wikipedia.org/wiki/Kartesisches_Koordinatensystem), die in der Regel **rechtshändig** vorausgesetzt werden.

<!-- style="background-color: lightgray;"-->
> Ein **kartesisches Koordinatensystem** besitzt ein Orthonormalsystem von **Basisvektoren** $(\underline{r}_1,\underline{r}_2,\underline{r}_3)$. Diese erfüllen die Gleichungen $$
        \underline{r}_j\cdot\underline{r}_k=\delta_{jk}:=\left\{\begin{array}{rcl} 1 & \text{für} & j=k \\ 0 & \text{für} & j\not=k \end{array}\right. \quad\text{und}\quad \det{(\underline{r}_1,\underline{r}_2,\underline{r}_3)}=1 $$
> worin $\delta_{jk}$ das [Kroneckersymbol](https://mathepedia.de/Kronecker-Delta.html) bezeichnet.

**Beispiel 1.** Die Lage eines Punktes im dreidimensionalen Punktraum wird auf ein kartesisches Koordinatensystem bezogen. Bezeichnet $(\underline{e}_1,\underline{e}_2,\underline{e}_3)$ die Orthonormalbasis des Koordinatensystems, so ist der Koordinatenvektor des Punktes ~~eindeutig~~ durch die (reelle) Linearkombination $$
        \underline{p}=(p_1,p_2,p_3)^\top:=p_1\cdot\underline{e}_1+p_2\cdot\underline{e}_2+p_3\cdot\underline{r}_3=\sum_{k=1}^3{(p_k\cdot \underline{e}_k)} $$
festgelegt, worin $p_k$ seine kartesischen Koordinaten bezüglich $(\underline{e}_1,\underline{e}_2,\underline{e}_3)$ bezeichnen.

**Beispiel 2.** Ein Punkt auf einer Verbindungsgeraden zweier Punkte mit den Koordinatenvektoren $\underline{a}$ und $\underline{b}$ ist durch die affine Linearkombination $$
        \underline{p}=(1-s)\cdot\underline{a}+s\cdot\underline{b} $$
mit der affinen Koordinate $t\in\mathbb{R}$ beschrieben.

---

### Basiswechsel

---

Betrachtet werden zwei Koordinatensysteme um den ~~gemeinsamen~~ Koordinatenursprung in $\mathbb{R}^3$. **Rast-** und **Gangkoordinatensystem** besitzen die Orthonormalbasen $$
        (\underline{r}_1,\underline{r}_2,\underline{r}_3)\quad\text{bzw.}\quad(\underline{g}_1,\underline{g}_2,\underline{g}_3) $$
vergleiche Folie [Euklidischer Raum](#euklidischer-raum).

Stelle die Basisvektoren $\underline{g}_i$ als Linearkombinationen der Basisvektoren $\underline{r}_j$ $$
        \underline{g}_i=\sum_{j=1}^3{(b_{ij}\cdot\underline{r}_j)}\,,\quad i\in\{1,2,3\} $$
mit zu bestimmenden reellen Koeffizienten $b_{ij}$ dar. Diese ergeben sich aus den Skalarprodukten $$
        \underline{g}_i\cdot\underline{r}_k=\sum_{j=1}^3\sum_{j=1}^3{\left[(b_{ij}\cdot(\underline{r}_j\cdot\underline{r}_k)\right]}=b_{ij}\cdot\delta_{jk}=b_{ik} $$
Somit gilt für den Übergang $$
        (\underline{g}_1,\underline{g}_2,\underline{g}_3)=(\underline{r}_1,\underline{r}_2,\underline{r}_3)\cdot\begin{pmatrix} \underline{g}_1\cdot\underline{r}_1 & \underline{g}_2\cdot\underline{r}_1 & \underline{g}_3\cdot\underline{r}_1 \\ \underline{g}_1\cdot\underline{r}_2 & \underline{g}_2\cdot\underline{r}_2 & \underline{g}_3\cdot\underline{r}_2 \\ \underline{g}_1\cdot\underline{r}_3 & \underline{g}_2\cdot\underline{r}_3 & \underline{g}_3\cdot\underline{r}_3 \end{pmatrix}=:(\underline{r}_1,\underline{r}_2,\underline{r}_3)\cdot A \quad\leftrightarrow\quad A=(\underline{r}_1,\underline{r}_2,\underline{r}_3)^\top\cdot(\underline{g}_1,\underline{g}_2,\underline{g}_3) $$ 
mit der reellen Matrix $A$.

---

>**Satz 1.** Die Matrix $A$ ist orthogonal, d. h. $A\cdot A^\top=A^\top\cdot A=E\;\leftrightarrow\; A^{-1}=A^\top$, worin $E$ die dreireihige Einheitsmatrix bezeichnet.

**Beweisidee.** Stelle - analog oben - umgekehrt die Basisvektoren $\underline{r}_i$ als Linearkombinationen der Basisvektoren $\underline{g}_j$ dar und berechne die Matrix des Basiswechsels.

---

>**Folgerung 2.** Die Gangkoordinaten $(\xi_1,\xi_2,\xi_3)\in\mathbb{R}^3$ eines Punktes im Gangkoordinatensystem $(\underline{g}_1,\underline{g}_2,\underline{g}_3)$ transformieren sich vermöge $$
        (x_1,x_2,x_3)^\top=A\cdot(\xi_1,\xi_2,\xi_3)^\top\quad\text{mit}\quad A=(\underline{r}_1,\underline{r}_2,\underline{r}_3)^\top\cdot(\underline{g}_1,\underline{g}_2,\underline{g}_3) $$
> in seine Rastkoordinaten $(x_1,x_2,x_3)\in\mathbb{R}^3$ bezogen auf das Rastkoordinatensystem $(\underline{r}_1,\underline{r}_2,\underline{r}_3)$.

---

Nachfolgend ist unter Verwendung der Programmiersprache [Octave](https://www.octave.org) die Funktion `basistransformation()` definiert, mit der zu zwei gegebenen Orthonormalbasen $B_j$ die Matrix für den Basiswechsel $B_2$ nach $B_1$ berechnet wird. Im Script 'main.m' können beide Basen gewählt und die Funktion mit beiden als Inputvariable ausgeführt werden. 

Variieren Sie im angegebenen Beispiel die Orthonaromalbasen und führen Sie die Funktion aus. Welchen Tests wird die Eingabe unterzogen?

---

```octave -basistransformation.m
% Definition der Funktion
function T = basistransformation (B1, B2, eps)

  if (!isnumeric(B1) || !isnumeric(B2))
    error("@basistransformation: Eingabeparameter müssen Matrizen sein!");
  endif

  if (norm(B1*transpose(B1)-eye(3), "fro") >= eps)
    error("@basistransformation: erstes Argument muss orthogonal sein!");
  endif

  if (norm(B2*transpose(B2)-eye(3), "fro") >= eps)
    error("@basistransformation: zweites Argument muss orthogonal sein!");
  endif

  if (abs(det(B1) - 1.0) >= eps)
    error("@basistransformation: Determinante des ersten Arguments ist nicht gleich 1!");
  endif

  if (abs(det(B2) - 1.0) >= eps)
    error("@basistransformation: Determinante des zweiten Arguments ist nicht gleich 1!");
  endif

  T = zeros(3, 3);
  for i = 1:3
    for j = 1:3
      T(i,j) = dot(B1(:,i), B2(:,j));
    endfor
  endfor

endfunction
```
```octave +main.m
% Definition der Basen
B1 = [1, 0, 0; 0, 1, 0; 0, 0, 1];
theta = pi/4;
B2 = [cos(theta), -sin(theta), 0; sin(theta), cos(theta), 0; 0, 0, 1];

eps = 1.0E-09;

% Aufruf der Funktion
T = basistransformation(B1, B2, eps);
disp("Matrix T des Basiswechsels (von B1 nach B2):");
disp(T);

% Koordinatentransformation
x = [5; -2; 1];
y = T * x;
disp("Koordinatenvektor bezüglich B2 transormiert sich in B1:")
disp(y);
```
@LIA.eval(`["basistransformation.m", "main.m"]`, `none`, `octave -q --no-window-system main.m`)

---

### Rotationen in 3D

---

<!-- style="background-color: lightgray;"-->
>Betrachte die durch den Basiswechsel festgelegte Transformation $\Phi:\mathbb{R}^3\to\mathbb{R}^3$ von Punkten des dreidimensionalen Raumes mit 
>
>* Koordinatenursprung $\underline{o}$ ist fest unter $\Phi$
>* Basisvektoren $\underline{r}_j$ werden auf die Basisvektoren $\underline{g}_j$ abgebildet
>
>Ein Punkt mit den Rastkoordinaten $\underline{x}=(\xi_1,\xi_2,\xi_3)^\top$ wird dann unter $\Phi$ auf den Punkt mit den Rastkoordinaten $\underline{x}'=(x_1,x_2,x_3)^\top$ vermöge $$
        (x_1,x_2,x_3)^\top=A\cdot (\xi_1,\xi_2,\xi_3)^\top\quad\text{mit}\quad A\cdot A^\top=E\quad\text{und}\quad \det{A}=1 $$
>abgebildet. 

Die Transformation $\Phi$ beschreibt eine **orientierungserhaltende Kongruenz** beziehungsweise - Isometrie, d. i. eine Abbildung, bei der 

* alle Distanzen invariant bleiben, d. h. $|\underline{x}-\underline{y}|=|\underline{x}'-\underline{y}'|$ für alle $\underline{x}\in\mathbb{R}^3$ und alle $\underline{y}\in\mathbb{R}^3$.
* alle Winkel zwischen Richtungen invariant bleiben.
* die Orientierungen in einem Körper sich nicht ändern.

<!-- style="color: purple;"-->
Transformationen dieser Art treten in der Robotik als **Starrkörperbewegungen** auf.

---

>**Satz 1.** Für eine orthogonale Matrix $A$ mit $\det{A}=1$ ist die Matrix $A-E$ singulär. $E$ beschreibt wie zuvor die dreireihige Einheitsmatrix.

Welche Punkte $\underline{x}$ bleiben unter der Transformation $\Phi$ invariant, sind also Fixpunkte der Transformation?

1. Für den Koordinatenursprung gilt $(0,0,0)^\top=A\cdot(0,0,0)^\top$, d. h. der Koordinatenursprung ist ein Fixpunkt der Abbildung.
2. Aus dem Eigenwertansatz zur Matrix $A$ mit $(A-E)\cdot\underline{x}=\underline{o}$ folgt aus Satz 1, dass $\lambda=1$ Eigenwert von $A$ ist und dass der Eigenvektorraum zu diesem Eigenwert mindestens die Dimension 'Eins' besitzt. 

>**Folgerung 2.** Gegeben ist eine Transformation $\Phi:\mathbb{R}^3\to\mathbb{R}^3$ von Punkten des dreidimensionalen Raumes mit $$
        (x_1,x_2,x_3)^\top=A\cdot (\xi_1,\xi_2,\xi_3)^\top\quad\text{mit}\quad A\cdot A^\top=E\quad\text{und}\quad \det{A}=1 $$
>
> 1. Für $A\not=E$ ist die Transformation eine Drehung, für $A=E$ die identische Abbildung.
> 2. Die Eigenwerte von $A$ sind $1$ sowie $\cos{\varphi}\pm i\cdot\sin{\varphi}$, wenn $\varphi\in\mathbb{R}$ Bogenmaß des Drehwinkels und $i^2=-1$.
> 3. Die Eigenvektoren zum Eigenwert $1$ sind die Koordinatenvektoren der Punkte der Drehachse.

---

**Beispiel.** Drehung um die dritte Koordinatenachse mit Drehwinkel $\varphi$. Die Transformationsmatrix berechnet sich zu $$
        A=\begin{pmatrix} \cos{\varphi} & -\sin{\varphi} & 0 \\ \sin{\varphi} & \cos{\varphi} & 0 \\ 0 & 0 & 1 \end{pmatrix} $$

* Aus dem Eigenwertansatz berechnet sich $$
        \det{(A-E)}=(1-\lambda)\cdot(\cos{\varphi}-\lambda)^2+(1-\lambda)\cdot\sin{\varphi}=(1-\lambda)\cdot(\lambda^2-2\lambda\cdot\cos{\varphi}+1)=0 $$ mit den Lösungen $\lambda_1=1$ und $\lambda_{2,3}=\cos{\varphi}\pm i\cdot\sin{\varphi}$.
* Für $\lambda_1$ berechnet sich der Eigenvektorraum als Lösung des Systems linearer Gleichungen $$
        \begin{pmatrix} \cos{\varphi}-1 & -\sin{\varphi} & 0 \\ \sin{\varphi} & \cos{\varphi}-1 & 0 \\ 0 & 0 & 0 \end{pmatrix}\cdot\begin{pmatrix} \xi_1 \\ \xi_2 \\ \xi_3 \end{pmatrix}=\begin{pmatrix} 0 \\ 0 \\ 0 \end{pmatrix}\quad\leadsto\quad \begin{pmatrix} \xi_1 \\ \xi_2 \\ \xi_3 \end{pmatrix}=s\cdot\begin{pmatrix} 0 \\ 0 \\ 1 \end{pmatrix} $$ mit $s\in\mathbb{R}$.

**Bemerkung.** Analog zum vorstehenden Beispiel beschreiben die Matrizen $$
        A_1=\begin{pmatrix} 1 & 0 & 0 \\ 0 & \cos{\varphi} & -\sin{\varphi} \\ 0 & \sin{\varphi} & \cos{\varphi} \end{pmatrix}\,,\quad
        A_2=\begin{pmatrix} \cos{\varphi} & 0 & \sin{\varphi} \\ 0 & 1 & 0 \\ -\sin{\varphi} & 0 & \cos{\varphi} \end{pmatrix}\quad\text{und}\quad
        A_3=\begin{pmatrix} \cos{\varphi} & -\sin{\varphi} & 0 \\ \sin{\varphi} & \cos{\varphi} & 0 \\ 0 & 0 & 1 \end{pmatrix} $$
Drehungen um die $x_1$-, die $x_2$- bzw. um die $x_3$-Achse des gegebenen Koordinatensystems in $\mathbb{R}^3$.

---

### Koordinatenwechsel

---

Für den Wechsel zweier kartesischer Koordinatensysteme mit verschiedenen Ursprüngen gilt:

<!-- style="background-color: lightgray;"-->
>**Koordinatenwechsel.** Bezeichnen respektive $$
  \mathfrak{G}=(\underline{u}\,|\,\underline{g}_1,\underline{g}_2,\underline{g}_3)\quad\text{und}\quad\mathfrak{R}=(\underline{o}\,|\,\underline{r}_1,\underline{r}_2,\underline{r}_3) $$
>das Gangkoordinatensystem und Rastkoordinatensystem mit verschiedenem Ursprung $\underline{u}\not=\underline{o}$ und bezeichnen $$
  \underline{x}_0=(x_1,x_2,x_3)^\top\quad\text{bzw.}\quad\underline{x}_1=(\xi_1,\xi_2,\xi_3)^\top $$
>Rastkoordinaten bzw. Gangkoordinaten eines Punktes $\underline{x}$, so ergibt sich aus Abschnitt [Basiswechsel](#Basiswechsel) die Darstellung $$
  \underline{x}_0=\underline{u}_0+A\cdot\underline{x}_1\quad\text{mit}\quad A\in\mathbb{R}^{(3,3)}\,,\;A\cdot A^\top=E\,,\;\det{A}=1 $$
>für den Koordinatenwechsel $\mathfrak{G}\to\mathfrak{R}$. Hierin sind $\underline{u}_0$ die Rastkoordinaten von $\underline{u}$ mit $$
  \underline{x}_0=\underline{u}_0+A\cdot\underline{o}=\underline{u}_0 $$

Für den Differenzvektor zweier Punkte $\underline{x}$ und $\underline{y}$ gilt $$
  \underline{v}_1=\underline{y}_1-\underline{x}_1\quad\text{sowie}\quad
  \underline{v}_0=\underline{y}_0-\underline{x}_0=A\cdot(\underline{y}_1-\underline{x}_1)=A\cdot\underline{v}_1 $$
d. h. die Transformation eines freien (Richtungs-) Vektors ist linear unter dem Wechsel $\mathfrak{G}\to\mathfrak{R}$.

---

**Bemerkung 1.** Die Anteile $\underline{u}_0$ und $A$ des Koordinatenwechsels können als [lineare Abbildung](https://de.wikipedia.org/wiki/Lineare_Abbildung) miteinander kombiniert werden. $$
  \begin{pmatrix} \underline{x}_0 \\ \textcolor{purple}{1} \end{pmatrix}=\begin{pmatrix} A & \underline{u}_0 \\ \textcolor{purple}{\underline{o}^\top} & \textcolor{purple}{1} \end{pmatrix}\cdot\begin{pmatrix} \underline{x}_1 \\ \textcolor{purple}{1} \end{pmatrix} $$
für die Koordinatentransformation von Ortsvektoren beziehungsweise $$
  \begin{pmatrix} \underline{v}_0 \\ \textcolor{purple}{0} \end{pmatrix}=\begin{pmatrix} A & \underline{o} \\ \textcolor{purple}{\underline{o}^\top} & \textcolor{purple}{1} \end{pmatrix}\cdot\begin{pmatrix} \underline{v}_1 \\ \textcolor{purple}{0} \end{pmatrix} $$
für die Transformation von freien Vektoren (Richtungen). Vektoren und Transformationsmatrix werden hierbei jeweils um eine **zusätzliche Zeile**<!-- style="color: purple;"--> erweitert[^1], so dass Matrixmultiplikation und Vektoraddition auf eine einzige Matrixmultiplikation reduziert werden kann. Die erweiterte Matrix vom Typ $(4,4)$ wird geränderte Matrix genannt, die in vorstehenden Formelzeilen angeführten Matrix-Vektor-Produkte **homogene Darstellung** des Koordinatenwechsels.

Die Spalten der geränderten Matrix $$
  \begin{pmatrix} \underline{u}_0 \\ 1 \end{pmatrix}\quad\text{bzw.}\quad \begin{pmatrix} A[j] \\ 0 \end{pmatrix} $$
für die $j$-te Spalte von $A$ beschreiben die **geränderten Rastkoordinaten** des Koordinatenursprungs $\underline{u}$ beziehungsweise der Basisvektoren $\underline{g}_j=A[j]$ des Gangkoordinatensystems $\mathfrak{G}$.

---

**Beispiel 1.** Zur Beschreibung kinematischer Ketten werden durch Rotationsgelenke miteinander gekoppelte, benachbarte Glieder mit kartesischen Koordinatensystemen verknüpft.

* Geht hierbei $\underline{u}$ aus $\underline{o}$ durch Verschiebung entlang $\underline{r}_1$ um den (festen) Betrag $d\in\mathbb{R}$ hervor und
* wird das Gangkoordinatensystem um $\underline{r}_1$ um den Winkel $\varphi\in[0,2\pi)$ gedreht

so berechnet sich der Koordinatenwechsel $\mathfrak{G}\to\mathfrak{R}$ gemäß $$
  \underline{x}_0=\underline{u}_0+A\cdot\underline{x}_1\quad\text{mit}\quad \underline{u}_0=\begin{pmatrix} 0 \\ 0 \\ d \end{pmatrix}\quad\text{sowie}\quad A=\begin{pmatrix} 1 & 0 & 0 \\ 0 & \cos{\varphi} & -\sin{\varphi} \\ 0 & \sin{\varphi} & \cos{\varphi} \end{pmatrix} $$
mit $\varphi\in[0,2\pi)$. In homogener Schreibweise stellt sich der Koordinatenwechsel gemäß $$
  \left(\begin{array}{c} \underline{x}_0 \\ \textcolor{purple}{1} \end{array}\right) = \begin{pmatrix} 1 & 0 & 0 & 0 \\ 0 & \cos{\varphi} & -\sin{\varphi} & 0 \\ 0 & \sin{\varphi} & \cos{\varphi} & d \\ \textcolor{purple}{0} & \textcolor{purple}{0} & \textcolor{purple}{0} & \textcolor{purple}{1} \end{pmatrix}\cdot \left(\begin{array}{c} \underline{x}_1 \\ \textcolor{purple}{1} \end{array}\right) $$
Diese Matrizen werden beim Koordinatenwechsel zwischen benachbarten Gliedern einer kinematischen Kette in der direkten kinematischen Aufgabe verwendet.

---

[^1]: Die Zeile wird im ingenieurwissenschaftlichen Kontext oft "am Ende" ergänzt. Diesem Vorgehen werden wir im Weiteren Verlauf der Vorlesung folgen.

### Schraubungen

---

Analog zuvor lässt sich auch hier die Formel für den Koordinatenwechsel als Bewegung des dreidimensionalen Raumes deuten, die das Rastkoordinatensystem $\mathfrak{R}$ mit dem Gangkoordinatensystem $\mathfrak{G}$ zur Deckung bringt, vergleiche Abschnitt [Rotationen in 3D](#Rotationen-in-3D).

<!-- style="background-color: lightgray;"-->
> **Bewegungen in 3D**. Die durch die Abbildung $$ \kappa:\mathfrak{R}\to\mathfrak{G}\quad\text{mit}\quad\kappa(\underline{o})=\underline{u}\quad\text{sowie}\quad\kappa(\underline{r}_j)=\underline{g}_j\quad\text{und}\quad j\in\{1,2,3\} $$ bestimmte Bewegung des dreidimensionalen Raumes bildet jeden mit $\mathfrak{R}$ fest verbundenen Punkt $\underline{x}$ mit den Rastkoordinaten $(\xi_1,\xi_2,\xi_3)$ ab auf einen Punkt $\underline{x}'$ mit den Rastkoordinaten $$
  \left[\begin{array}{c} x_1 \\ x_2 \\ x_3 \end{array}\right]_0 = \left[\begin{array}{c} u_1 \\ u_2 \\ u_3 \end{array}\right]_0+A\cdot\left[\begin{array}{c} \xi_1 \\ \xi_2 \\ \xi_3 \end{array}\right]_0 $$
> worin $A\cdot A^\top=E$ mit dreireihiger Einheitsmatrix $E$ und $\det{A}=1$ gelten.


**Beispiel 1.** Für die im Abschnitt [Rotationen in 3D](#Rotationen-in-3D) genannten Matrizen zur Beschreibung von Drehungen um die Koordinatenachsen im $\mathbb{R}^3$ lassen sich die geränderten Matrizen wie in nachstehender Tabelle bilden.

| Drehachse   | Drehwinkel   | Rotationsmatrix   | homogene Darstellung   |
| :--------- | :--------- | :--------- | :--------- |
| $x_1$-Achse     | $\varphi$     | $A_1=\begin{pmatrix} 1 & 0 & 0 \\ 0 & \cos{\varphi} & -\sin{\varphi} \\ 0 & \sin{\varphi} & \cos{\varphi} \end{pmatrix}$     | $\utilde{A}_1=\begin{pmatrix} 1 & 0 & 0 & \textcolor{black}{0} \\ 0 & \cos{\varphi} & -\sin{\varphi} & \textcolor{black}{0} \\ 0 & \sin{\varphi} & \cos{\varphi} & \textcolor{black}{0} \\ \textcolor{purple}{0} & \textcolor{purple}{0} & \textcolor{purple}{0} & \textcolor{purple}{1} \end{pmatrix}$     |
| $x_2$-Achse     | $\varphi_2$     | $A_2=\begin{pmatrix} \cos{\varphi_2} & 0 & \sin{\varphi_2} \\ 0 & 1 & 0 \\ -\sin{\varphi_2} & 0 & \cos{\varphi_2} \end{pmatrix}$     | $\utilde{A}_2=\begin{pmatrix} \cos{\varphi_2} & 0 & \sin{\varphi_2} & \textcolor{black}{0} \\ 0 & 1 & 0 & \textcolor{black}{0} \\ -\sin{\varphi_2} & 0 & \cos{\varphi_2} & \textcolor{black}{0} \\ \textcolor{purple}{0} & \textcolor{purple}{0} & \textcolor{purple}{0} & \textcolor{purple}{1} \end{pmatrix}$     |
| $x_3$-Achse     | $\varphi_3$     | $A_2=\begin{pmatrix} 1 & 0 & 0 \\ \cos{\varphi_3} & -\sin{\varphi_3} & 0 \\ \sin{\varphi_3} & \cos{\varphi_3} & 0 \end{pmatrix}$     | $\utilde{A}_3=\begin{pmatrix}  1 & 0 & 0 & 0\\ \cos{\varphi_3} & -\sin{\varphi_3} & 0 & 0\\ \sin{\varphi_3} & \cos{\varphi_3} & 0 & 0 \\ \textcolor{purple}{0} & \textcolor{purple}{0} & \textcolor{purple}{0} & \textcolor{purple}{1} \end{pmatrix}$     |

**Beispiel 2.** Für eine reine Translation $\tau$ von $\mathfrak{R}$ in Richtung des Vektors $\underline{u}\in\mathbb{R}^3$ mit $\underline{u}\not=\underline{o}$ berechnet sich $$
  \left[\begin{array}{c} x_1 \\ x_2 \\ x_3 \end{array}\right]_0 = \left[\begin{array}{c} u_1 \\ u_2 \\ u_3 \end{array}\right]_0+E\cdot\left[\begin{array}{c} \xi_1 \\ \xi_2 \\ \xi_3 \end{array}\right]_0 $$
beziehungsweise in homogener Darstellung $$
  \left[\begin{array}{c} x_1 \\ x_2 \\ x_3 \\ \textcolor{purple}{1} \end{array}\right]_0 = \left(\begin{array}{cccc} 1 & 0 & 0 & u_1 \\ 0 & 1 & 0 & u_2 \\ 0 & 0 & 1 & u_3 \\ \textcolor{purple}{0} & \textcolor{purple}{0} & \textcolor{purple}{0} & \textcolor{purple}{1} \end{array}\right)\cdot\left[\begin{array}{c} \xi_1 \\ \xi_2 \\ \xi_3 \\ \textcolor{purple}{1} \end{array}\right]_0 $$

Unter Verwendung der Programmiersprache [Octave](https://www.octave.org) können die Rastkoordinaten des Bildpunktes $\underline{y}$ unter einer Bewegung berechnet werden.

```octave
% Definition der Bewegung
phi = pi/4;
A = [cos(phi), -sin(phi), 0; sin(phi), cos(phi), 0; 0, 0, 1];
u = [1; 2; 3];

% Bewegung eines Punktes
x = [5; -2; 1];
y = u + A * x;
disp("Bewegung des Punktes x nach y:")
disp(y);
```
@LIA.eval(`["main.m"]`, `none`, `octave -q --no-window-system main.m`)

---

>**Definition 1.** Die Hintereinanderausführung (Komposition)
>
>1. einer *Drehung* um eine Gerade $d$ des dreidimensionalen Raumes mit Drehwinkel $\varphi$ bezogen auf einen Einheitsrichtungsvektor $\underline{d}$ von $d$ mit
>2. einer *Translation* entlang $d$ mit Schubvektor $\underline{u}=v\cdot\underline{d}$ mit $v\in\mathbb{R}$ heißt [Schraubung](https://de.wikipedia.org/wiki/Schraubung) $\sigma$.
>
> Für $v>0$ heißt die Schraubung $\sigma$ **rechtsgängig**, für $v<0$ heißt $\sigma$ **linksgängig**. Für $v=0$ ist die Schraubung $\sigma$ eine reine Drehung, wohin $\sigma$ für $\varphi=0$ und $v\not=0$ eine reine Translation ist.

Damit folgt der nachstehende Satz.

>**Satz 1.** In der Verallgemeinerung der betrachteten Beispiele ergeben sich:
>
>1. Jede Bewegung des dreidimensionalen Raumes ist darstellbar in der Form $$ \sigma:\underline{x}\mapsto\underline{x}'=\underline{u}+A\cdot\underline{x}\quad\text{mit}\quad A\in\mathbb{R}^{(3,3)}\,,\;A\cdot A^\top=E\,,\;\det{A}=1 $$
>2. Jede dieser Bewegungen $\sigma$ ist eine Schraubung, d. i. die Hintereinanderausführung einer Drehung um eine Gerade $d\subset\mathbb{R}^3$ und eine anschließende Translation entlang $d$.
>3. Ist $A\not=E$ (Einheitsmatrix) und $\underline{d}$ Einheitsvektor in Richtung der Schraubachse $d$ sowie $\varphi$ Bogenmaß des Drehwinkels, dann ist nach Abschnitt [Basiswechsel](#Basiswechsel) $A\cdot\underline{d}=\underline{d}$ und $$
        \underline{s}=\frac{1}{2}\cdot\left[\underline{u}-(\underline{u}\cdot\underline{d})\cdot\underline{d}+\cot{\left(\frac{\varphi}{2}\right)}\cdot(\underline{d}\times\underline{u})\right] $$ ist ein Punkt auf der Drehachse $d$.
>4. Für $A\not=E$ ist $\sigma$ genau dann eine Drehung, wenn $\underline{u}\cdot\underline{d}=0$.

**Bemerkung 1.** Die Schraubung $\sigma$ hat bezüglich eines beliebigen kartesischen Koordinatensystems die Darstellung $$
  \sigma:\underline{x}\mapsto\underline{x}'=A\cdot(\underline{x}-\underline{s})+\underline{s}+(\underline{u}\cdot\underline{d})\cdot\underline{d} $$
worin $\underline{s}$ einen Punkt auf der Schrubachse bezeichnet.

---

### SO(3) und SE(3)

---

In diesem Abschnitt werden Koordinatenwechsel beziehungsweise Bewegungen wiederholt hintereinander ausgeführt. Betrachtet werden insbesondere algebraische Eigenschaften dieser Komposition. Die algebraischen Strukturen sind insbesondere in der Robotik von Interesse.

Für die nachfolgenden Eigenschaften bezeichnen $\kappa_j$ beliebige Bewegungen des dreidimensionalen Raumes mit $$
  \underline{x}'=\underline{u}_j+A_j\cdot\underline{x}\quad\text{mit}\quad A_j\cdot A_j^\top=E\,,\;\det{A_j}=1\,,\;\underline{u}_j\in\mathbb{R}^3 $$
worin $j\in I\subset\mathbb{N}$.

---

Komposition
===

Die Hintereinanderausführung (Komposition) von '$\kappa_2$ nach $\kappa_1$' berechnet sich gemäß $$
  \kappa_2\circ\kappa_1:\; \underline{x}'=A_2\cdot(A_1\cdot\underline{x}+\underline{u}_1)+\underline{u}_2=(A_2\cdot A_1)\cdot\underline{x}+A_2\cdot\underline{u}_1+\underline{u}_2 $$
Der Summand $\underline{u}:=A_2\cdot\underline{u}_1+\underline{u}_2$ ist ein dreidimensionaler Vektor; daneben berechnet sich für das Matrizenprodukt $A:=A_2\cdot A_1$ $$
  A\cdot A^\top=(A_2\cdot A_1)\cdot(A_2\cdot A_1) \top=(A_2\cdot A_1)\cdot\left(A_1^\top\cdot A_1^\top\right)=A_2\cdot(A_1\cdot A_1^\top)\cdot A_2^\top=E $$
Darüber hinaus folgt mit dem Multiplikationssatz für Determinanten $$
  \det{A}=\det{(A_2\cdot A_1)}=\det{A_2}\cdot\det{A_1}=1 $$
Hieraus folgt, dass die Komposition $(\kappa_2\circ\kappa_1)$ wieder eine Bewegung des dreidimensionalen Raums beschreibt: Die Menge aller Bewgungen $\kappa$ des dreidimensionalen Raumes ist demnach abgeschlossen unter der Komposition.

---

Umkehrabbildung
===

Es sei eine Bewegung des dreidimensionalen Raumes $\kappa:\underline{x}\mapsto\underline{x}'$ mit $\underline{x}'=A\cdot \underline{x}+\underline{u}$ wie zuvor festgelegt. Durch Multiplikation beider Seiten der Darstellung 'von links' mit $A^\top$ lässt sich $$
  A^\top\cdot \underline{x}'=A^\top\cdot(A\cdot \underline{x}+\underline{u})=(A^\top\cdot A)\cdot \underline{x}+A^\top\cdot\underline{u}\quad\leftrightarrow\quad \underline{x}=A^\top\cdot\underline{x}'-A^\top\cdot\underline{u} $$
Dabei beschreibt $B=A^\top$ mit $\det{B}=1$ eine Drehung, die Abbildung $\kappa^{-1}:\underline{x}'\mapsto\underline{x}$ wieder eine Bewegung des dreidimensionalen Raumes.

---

Assoziativität
===

Die Komposition $\kappa_3\circ(\kappa_2\circ\kappa_1)$ berechnet sich unter Nutzung des obigen Ergebnisses $$
  \underline{x}'=A_3\cdot((A_2\cdot A_1)\cdot\underline{x}+A_2\cdot\underline{u}_1+\underline{u}_2)+\underline{u}_3 = (A_3\cdot A_2\cdot A_1)\cdot\underline{x}+(A_3\cdot A_2)\cdot\underline{u}_1+A_3\cdot\underline{u}_2+\underline{u}_3 $$
analog berechnet sich $(\kappa_3\circ\kappa_2)\circ\kappa_1$ analog $$
  \underline{x}'=(A_3\cdot A_2)\cdot(A_1\cdot\underline{x}+A_2\cdot\underline{u}_1)+A_3\cdot\underline{u}_2+\underline{u}_3 = (A_3\cdot A_2\cdot A_1)\cdot\underline{x}+(A_3\cdot A_2)\cdot\underline{u}_1+A_3\cdot\underline{u}_2+\underline{u}_3 $$
Dies zeigt, dass die Komposition assoziativ ist.

---

Keine Kommutativität
===

Aus der Bildung von '$\kappa_1$ nach $\kappa_2$' folgt unmittelbar $\kappa_2\circ\kappa_1\stackrel{i. A.}{\not=}\kappa_1\circ\kappa_2$ Die Komposition ist somit **nicht kommutativ**.

---

Identische Abbildung
===

Die Abbildung $\underline{x}'=E\cdot\underline{x}+\underline{o}$ mit dreireihiger Einheitsmatrix $E$ und dreidimensionalem Nullvektor $\underline{o}$heißt **identische Abbildung**.

---

Damit gilt schließlich der nachstehende Satz.

>**Satz 1.** Die Menge aller Bewegungen des dreidimensionalen Raumes bildet bezüglich der Komposition eine **Gruppe**, die spezielle euklidische Gruppe $SE(3)$.

**Bemerkung 1.** Ebenso bildet die Menge aller Drehungen um Geraden durch den Koordinatenursprung $d\ni\underline{u}$ eine **Untergruppe** von $SE(3)$, die spezielle orthogonale Gruppe $SO(3)<SE(3)$.

