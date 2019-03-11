<style>
img.center {
  width: 75%;
}
</style>

# Miglioramenti
Ci sono dei miglioramenti che possiamo fare al codice scritto finora, per renderlo più generale e facilmente utilizzabile.

## Parametri del costruttore
Guardiamo da vicino il costruttore della nostra classe:

```java
Personaggio() {
  forma = createShape(RECT, 0, 0, 30, 30);
  forma.setFill(color(255,0,0));

}
```

La classe `Personaggio` in questo momento crea tutti oggetti con la forma di un quadrato 30x30 di colore rosso. Se volessimo creare dei personaggi di forma e colore diverso? Voler generalizzare una funzione è un problema comune e la soluzione è semplice: possiamo _parametrizzare_ la funzione, rendendola più generica.

> Questa tecnia la applicheremo al costruttore ma si può utilizzare per qualsiasi funzione.

Dobbiamo fare i seguenti passi:
1. creare dei parametri all'interno delle parentesi tonde nella dichiarazione della funzione, specificando _tipo_ e _nome_ del parametro
1. sostituire i valori costanti all'interno della funzione con il nuovo parametro
1. aggiungere i parametri necessari quando viene chiamata la funzione

Nel nostro caso, ipotizziamo di voler avere la possibilità cambiare la dimensione ed il colore del personaggio:

1. aggiungo i parametri nella dichiarazione del costruttore:

```java
Personaggio(float siz, color col) {
   //...
}
```

2. sostituisco le costanti con i nuovi all'interno della funzione:
```java
Personaggio(float siz, color col) {
    forma = createShape(RECT, 0, 0, siz, siz);
    forma.setFill(col);
}
```

3. aggiungo i parametri necessari quando creo l'oggetto:

```java
cappuccetto = new Personaggio(30, color(255,0,0));
```

Ora posso creare nuovi personaggi con forma e colore diverso, semplicemente cambiando i parametri quando creo l'oggetto.

## Variabili di posizione
C'è ancora qualcosa che posso migliorare. Ragionandoci, la variabile `xCappuccetto` rappresenta la posizione del personaggio di cappuccetto rosso, e quindi fa parte delle caratteristiche del personaggio. Se in seguito dovessi creare il personaggio della nonna, vorrei che anche lei avesse una propria posizione.

Sposto quindi la variabile `xCappuccetto` da fuori a _dentro_ la classe `Personaggio`, cambiandogli nome nel più generico `x`.

```java
class Personaggio {
  //...
  int x = 10; // tutti i personaggi iniziano dalla posizione 10
  // ...
}
```

Quando disegno il personaggio, sostituisco la variabile di posizione con questa nuova:
```java
class Personaggio {
  //...
  void disegna() {
      shape(forma, x, height*0.5);
  }
}
```

Infine, quando cambio la posizione del personaggio, devo ricordarmi di sostituire la vecchia variabile con la nuova:

```java
if (cappuccetto.x < width*0.8) {
    cappuccetto.x = cappuccetto.x + 5;
  }
```

Come potete vedere, anche in questo caso ho usato la notazione con il punto per accedere alla variabile interna alla classe.

## Codice finale
Di seguito il codice completo finora.

```java
Personaggio cappuccetto;
PShape house;


class Personaggio {
  PShape forma;
  int x = 10;
  Personaggio(float siz, color col) {
    forma = createShape(RECT, 0, 0, siz, siz);
    forma.setFill(col);
  }
  void disegna() {
    shape(forma, x, height*0.5);
  }
};

void setup() {
  fullScreen(); // usa tutto lo schermo

  cappuccetto = new Personaggio(30, color(255,0,0));

  house = createShape(RECT, 0, 0, 100, 100);
}

void draw() {
  background(#00FF00); // siamo nella foresta, lo sfondo è verde

  //disegniamo la casa a destra, a metà altezza dello schermo
  shape(house, width*0.8, height*0.5);

  cappuccetto.disegna();
  // incremento la coordinata x di cappuccetto
  // solo se non è ancora nella casa della nonna
  if (cappuccetto.x < width*0.8) {
    cappuccetto.x = cappuccetto.x + 5;
  }
}
```
