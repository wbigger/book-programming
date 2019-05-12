<style>
.left_side {
  float:left;
  margin:5px 20px 0px 5px;
}
.right_side {
  float:left;
  margin:5px 20px 0px 5px;
}
p.clear {
  clear: both;  
}
</style>

# Costrutti ciclici

Cappuccetto Rosso deve passare attraverso una foresta, ma finora nella nostra applicazione c'è solo un grande prato. Come facciamo a disegnare tanti alberi?

<img class="right_side" title="La foresta nera" alt="La foresta nera" src="assets/foresta-nera.jpg" width="20%">

Cappuccetto Rosso è una storia popolare di varie parti del mondo, ma possiamo far riferimento alla versione dei Fratelli Grimm ed immaginarci che la bambina debba attraversare la foresta nera, formata da una fitta vegetazione di abeti. Per cominciare, proviamo a disegnare la foresta con gli strumenti che abbiamo visto finora.
<p class="clear"></p>

## Definizione della classe Pianta
Come abbiamo fatto finora, creiamo una nostra classe `Pianta` per rappresentare un albero.

> Abbiamo scelto `Pianta` ma anche `Albero` o `Abete` potevano essere delle scelte accettabili; dipende molto da cosa immaginiamo possa servirci in futuro. In ogni caso, l'importante è che la scelta sia sensata _adesso_, in futuro possiamo sempre cambiare nome (questa operazione si chiama _refactoring_). Da evitare assolutamente invece un nome che al momento non ha molto senso ma prevediamo che possa averlo in futuro; in questo modo infatti stiamo creando un programma inconsistente, con probabili conseguenze catastrofiche.

Che caratteristiche ha la nostra pianta? Ha sicuramente una forma ed una posizione nella foresta; inoltre ci piacerebbe che le diverse piante abbiano una dimensione diversa, quindi aggiungiamo anche un attributo per la dimensione. Altra domanda: cosa possiamo fare con la nostra pianta? Diciamo che possiamo seminarla per farla comparire nella nostra foresta.

Ecco quindi una possibile definizione di questa classe.

```java
class Pianta {
  // All'inizio mettiamo gli attributi della classe, ovvero le sue caratteristiche
  PShape forma;
  float x, y;
  float dimensione;

  // Subito dopo, scriviamo il costruttore
  Pianta(String piantaObj) {
    // assegniamo la dimensione in modo casuale
    dimensione = random(30, 80);
    // carichiamo il modello della nostra pianta, passandogli come parametro il file con il modello
    forma = loadShape(piantaObj);
    // scaliamo l'oggetto
    forma.scale(dimensione);
    // ruotiamolo lungo l'asse X per rispettare le convenzioni di Processing
    forma.rotateX(radians(180));
    // ruotiamolo casualmente lungo l'asse verticale, per dare un po' di vivacità alla foresta
    forma.rotateY(radians(random(-45,45)));

    // opzionale: coloriamo la pianta di verde. Utile se il modello non è già colorato di suo
    forma.setFill(#28C61E);

    // assegniamo una posizione casuale nello schermo
    x = random(0, width);
    y = random(0, height);
    // spostiamo la pianta nella nuova posizione
    forma.translate(x,y);
  }


  void semina() {
    // disegniamo la pianta
    shape(forma);
  }
}
```

OK, ora abbiamo la nostra pianta. Ora vediamo come disegnarne tante per creare una foresta.

## Versione senza cicli
Immaginiamo di voler aggiungere 3 alberi. Come già sappiamo fare, creiamo tre variabili ed assegniamo ad ognuna di esse un nuovo albero.

```java
// Dichiarazione delle variabili
Pianta abete0;
Pianta abete1;
Pianta abete2;

void setup() {
  // [...]
  // Assegnazione delle variabili
  abete0 = new Pianta("abete.obj");
  abete1 = new Pianta("abete.obj");
  abete2 = new Pianta("abete.obj");
}

void draw() {
  // [...]
  // Disegno gli alberi
  abete0.semina();
  abete1.semina();
  abete2.semina();
}
```

Questo metodo funziona se abbiamo pochi alberi da disegnare. Ma se ne volessimo disegnare tanti, diventerebbe molto scomodo!

Per risolvere questo problema dobbiamo usare due nuovi strumenti che ci mette a disposizione Processing: gli array e i cicli. Cominciamo dagli array.

## Array
Avere tante variabili simili tra di loro `abete0`, `abete1`, etc. è scomodo ed è molto facile sbagliarsi. Sarebbe utile se potessimo dichiarare una sola variabile che contiene tutti gli alberi da disegnare, in modo da maneggiarla con più facilità. Questo in programmazione si può fare attraverso uno strumento che si chiama _array_.

In Processing (ovvero in Java) gli array sono una sequenza di oggetti tutti dello stesso tipo. Per dichiarare un array usiamo la seguente sintassi.

```java
// Dichiarazione array
Pianta[] arrayAbeti;
```

Come si vede, aggiungendo le parentesi quadre dopo il tipo base, la variabile dichiarata si trasforma in un array; in altre parole, un insieme ordinato di piante. Per ordinato intendiamo che le piante sono in sequenza: esiste una pianta identificata con il numero`0`, seguita da pianta identificata con `1` e così via.

Ora che abbiamo dichiarato la variabile, dobbiamo assegnarli un valore. Per fare questo, usiamo la seguente sintassi nella funzione `setup()`.

```java
void setup() {
  // [...]

  // Assegnazione di un array
  arrayAbeti = new Pianta[3];
}
```

Prestate la massima attenzione a questa sintassi. Notiamo prima di tutto che nell'assegnazione dobbiamo ripetere la notazione con il tipo e le parentesi quadre. In questo caso però, dentro le parentesi aggiungiamo anche il numero di oggetti che vogliamo mettere nel nostro array, in questo caso tre.

Fate inoltre attenzione che questa assegnazione crea l'array, ovvero il contenitore, ma non i singoli alberi. Per creare i singoli alberi, dobbiamo comunque fare un assegnazione per ogni elemento dell'array.

```java
// Dichiarazione array
void setup() {
  // [...]
  // Assegnazione array
  arrayAbeti = new Pianta[3];
  // Assegnazione singole piante
  arrayAbeti[0] = new Pianta("abete.obj");
  arrayAbeti[1] = new Pianta("abete.obj");
  arrayAbeti[2] = new Pianta("abete.obj");
}
```

Fate attenzione che, convenzionalmente, il primo elemento dell'array è identificato con l'indice `0`. Ne segue che l'ultimo elemento sarà identificato con l'indice `n-1`, dove `n` è la lunghezza dell'array. Nel nostro caso `n` è 3 e quindi l'ultimo elemento avrà indice `2`.

Finora la situazione non è che sia migliorata molto, ma dobbiamo ancora mettere in campo il secondo strumento di cui abbiamo accennato: i cicli

# Ciclo for classico

Osserviamo bene queste righe:
```java
arrayAbeti[0] = new Pianta("abete.obj");
arrayAbeti[1] = new Pianta("abete.obj");
arrayAbeti[2] = new Pianta("abete.obj");
```

Notiamo che sono quasi del tutto identiche, tranne che per il numero dentro le parentesi quadre. Come facciamo a mettere a fattor comune la parte che si ripete? Con i cicli!

Ricapitoliamo cosa dobbiamo fare: ripetere un'istruzione per (in inglese `for`) ogni valore di un indice intero che varia da 0 a 2, incrementando ogni volta l'indice di una unità. Mettendo insieme queste cose, veniamo alla formulazione classica del ciclo for:

```java
for(int index = 0; index < 3; index = index + 1) {
  arrayAbeti[index] = new Pianta("abete.obj");
}
```

Ora possiamo aumentare il nostro numero di alberi semplicemente cambiando il valore 3 nell'assegnazione dell'array e nel ciclo. Ottimo!

Nota: possiamo migliorare leggermente il codice qui sopra, per evitare di sbagliare la dimensione dell'array con conseguenze spesso disastrose. Al posto del valore `3`, possiamo mettere `arrayAbeti.length`, che ci restituisce sempre il valore corretto della lunghezza dell'array. Miglioriamo quindi il ciclo scritto qui sopra nel seguente modo:

```java
for(int index = 0; index < arrayAbeti.length; index = index + 1) {
  arrayAbeti[index] = new Pianta("abete.obj");
}
```

# Enhanced for loop

Il caso particolare per cui dobbiamo ripetere delle istruzioni per tutti gli elementi di un array è molto comune e per questo Processing (ovvero Java) prevedono un costrutto particolare, più chiaro e conciso.

Nella funzione `draw()` possiamo usare la seguente sintassi per disegnare gli alberi.

```java
for (Pianta abete: arrayAbeti) {
  abete.semina();
}
```

Questo `for` chiama la funzione `.semina()` per tutti gli elementi dell'array. Come vedete, rispetto alla sintassi classica, questa versione "migliorata" (_enhanced_) è più breve, non rischiamo di sbagliare il valore di inizio o fine dell'indice e, soprattutto, rendiamo esplicito che vogliamo fare un'iterazione su un array. Questo è utile sia per gli altri sviluppatori, per comprendere meglio quello che sta facendo il codice, sia per il compilatore che in questo modo ha maggiori possibilità di ottimizzare il codice e renderlo più veloce.

# Codice finale
```java
Personaggio cappuccetto;

class Personaggio {
  PShape forma;
  // costruttore, chiamato quando viene usato "new"
  Personaggio(String filename) {
    forma = loadShape(filename);
    forma.setFill(color(255, 0, 0));
  }

  void disegna(float x, float y) {
    shape(forma, x, y);
  }
}

PShape house;
int xCappuccetto = 150;
// Dichiarazione array
Pianta[] arrayAbeti;

void setup() {
  fullScreen(P3D); // usa tutto lo schermo

  cappuccetto = new Personaggio("cappuccetto.obj");
  cappuccetto.forma.rotateX(radians(180));

  house = createShape(RECT, 0, 0, 100, 100);

  // Assegnazione array
  arrayAbeti = new Pianta[3];

  // Assegnazione singole piante
  for (int index = 0; index < arrayAbeti.length; index = index + 1) {
    arrayAbeti[index] = new Pianta("abete.obj");
  }
}

void draw() {
  background(#00FF00); // siamo nella foresta, lo sfondo è verde

  // accendiamo le luci per vedere gli oggetti in 3D
  lights();



  //disegniamo la casa a destra, a metà altezza dello schermo
  shape(house, width*0.8, height*0.5);

  //disegniamo cappuccetto a metà altezza dello schermo
  cappuccetto.disegna(xCappuccetto, height*0.5);

  // incremento la coordinata x di cappuccetto
  // solo se non è ancora nella casa della nonna
  if (xCappuccetto < width*0.8) {
    xCappuccetto = xCappuccetto + 5;
  }

  for (Pianta abete : arrayAbeti) {
    abete.semina();
  }
}
```
