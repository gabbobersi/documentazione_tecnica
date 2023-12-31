## FPS in generale
Rappresentano il numero di volte al secondo in cui disegnare sulla finestra.
Es. fps = 30, significa che vuoi disegnare 30 frame ogni secondo.

Il sistema degli FPS è strettamente relazionato al game loop design.\
Se gli sviluppatori spendono il 90% del loro tempo sul 10% del codice, il sistema degli FPS rientra in quel 10%!

#### METODO 1 - SLEEP
Sfrutto la funzionalità del thread di essere fermato in qualsiasi momento, per 
fermare l'esecuzione del game loop (che è ospitato in un thread) fino al prossimo disegno effettivo.

Questo sistema è considerato "basico" in quanto non tiene conto dei rallentamenti di fps.
```Java
class Game(){
    private boolean running = false;
    private long MILLISECONDS = 1000000000;

    public void run(){
        // Ogni quanto disegnare
        double drawInterval = MILLISECONDS / FPS;
        // Quando disegnare la prossima volta.
        double nextDrawTime = System.nanoTime() + drawInterval;

        while(running) {
            update();
            repaint();

            // Tempo rimanente fino a prossimo disegno.
            double remainingTime = nextDrawTime - System.nanoTime();
            // Converto i nanosecondi in millisecondi (accettati dal metodo sleep).
            remainingTime = remainingTime / 1000000;
            
            
            if (remainingTime < 0){
                remainingTime = 0;
            }
            // Fermo il thread per il tempo necessario.
            Thread.sleep((long) remainingTime);

            // Dopo aver atteso il tempo necessario, riparto con i calcoli ma stavolta con un nuovo obiettivo davanti.
            nextDrawTime += drawInterval;
        } 
    }
}
```
Avete notato il controllo se `remainingTime` è negativo?\
Ciò è dovuto al fatto che se l'update + repaint ci mettono più tempo rispetto al `nextDrawTime`, il `remainingTime`
diventa **negativo** (infatti `remainingTime = nextDrawTime - System.nanoTime()`!)

#### METODO 2 - DELTA (o "accumulatore")
Disegno/aggiorno solamente quando occorre farlo.
```Java
class Game(){
    private boolean running = false;
    private long MILLISECONDS = 1000000000;

    public void run(){
        // Ogni quanto disegnare
        double drawInterval = MILLISECONDS / FPS;
        double delta = 0;
        long lastTime = System.nanoTime();
        long currentTime;

        while(running){
            currentTime = System.nanoTime();
            delta += (currentTime - lastTime) / drawInterval;
            lastTime = currentTime;

            if (delta >= 1){
                update();
                repaint();
                delta--;
            }

        }      
    }
}
```
#### MOSTRARE GLI FPS A CONSOLE (riutilizzando l'esempio DELTA)

```Java
class Fps(){
    private boolean running = false;
    private long MILLISECONDS = 1000000000;
    
    public void run(){
        double drawInterval = MILLISECONDS / FPS;
        double delta = 0;
        long lastTime = System.nanoTime();
        long currentTime;
        
        // Aggiungo le variabili necessarie
        long timer = 0;
        int drawCount = 0;

        while(running){
            currentTime = System.nanoTime();
            delta += (currentTime - lastTime) / drawInterval;
            // Aggiungo il timer
            timer += (currentTime - lastTime);
            
            lastTime = currentTime;

            if (delta >= 1){
                update();
                repaint();
                delta--;
                drawCount++;
            }

            if(timer <= MILLISECONDS){
                System.out.println("FPS: " + drawCount);
                drawCount = 0;
                timer = 0;
            }
        }   
    }
}
```
