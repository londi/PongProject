# PongProject

```python
import pgzrun
import random
from gpiozero import MCP3008
import math

# Potentiometer Input Werte (Zwischen 0.000000000000000 und 1.000000000000000)
pot1 = MCP3008(0)
pot2 = MCP3008(1)

# Farben definieren
BLACK = (0  ,0  ,0  )
WHITE = (255,255,255)

# Start Spielstand
p1Score = p2Score = 0

# Ballgeschwindigkeit
BALLSPEED = 5

# Paddle Startposition
p1Y = 300
p2Y = 300

# Zeichnet das Spiel-Fenster mit der Netz-Linie, Spielstand-Nummern, Paddles und den Ball
def draw():
    screen.fill(BLACK)
    screen.draw.line((400,0),(400,600),"green")
    drawPaddles()
    drawBall()
    screen.draw.text(str(p1Score), center=(105, 40), color=WHITE, fontsize=60)
    screen.draw.text(str(p2Score), center=(705, 40), color=WHITE, fontsize=60)

# Aktualisiert die Paddle- und die Ball-Position
def update():
    updatePaddles()
    updateBall()

# Initialisiert das Spiel und laesst den Ball in eine zufaellige Richtung spielen
def init():
    global ballX, ballY, ballDirX, ballDirY
    ballX = 400
    ballY = 300
    initalStartDirection = random.randint(10, 350)
    while (initalStartDirection > 80 and initalStartDirection < 100) or (initalStartDirection > 260 and initalStartDirection < 280):
            initalStartDirection = random.randint(10, 350)
    ballDirX = math.cos(math.radians(initalStartDirection))
    ballDirY = math.sin(math.radians(initalStartDirection))

# Zeichnet die Paddles
def drawPaddles():
    global p1Y, p2Y

    p1rect = Rect((100, p1Y-30), (10, 60))
    p2rect = Rect((700, p2Y-30), (10, 60))

    screen.draw.filled_rect(p1rect, "red")
    screen.draw.filled_rect(p2rect, "red")

# Aktualisiert die Position der Paddles anhand der Potentiometer-Stellung
def updatePaddles():
    global p1Y, p2Y
    p1Y = (pot1.value * 540) + 30
    p2Y = (pot2.value * 540) + 30
    print("Spieler 1 Position: " + str(p1Y))
    print("Spieler 2 Position: " + str(p2Y))

# Aktualisiert die Richtung des Balles je nachdem ob der Ball eine Wand oder ein Paddle beruehrt hat
def updateBall():
    global ballX, ballY, ballDirX, ballDirY, p1Score, p2Score

    ballX += ballDirX * BALLSPEED
    ballY += ballDirY * BALLSPEED
    ballRect = Rect((ballX-4, ballY-4), (8,8))
    p1rect = Rect((100, p1Y-30), (10, 60))
    p2rect = Rect((700, p2Y-30), (10, 60))

    if checkCollide(ballRect, p1rect) or checkCollide(ballRect, p2rect):
            ballDirX *= -1
    if ballY < 4 or ballY > 596:
            ballDirY *= -1
    if ballX < 0:
            p2Score += 1
        init()
    if ballX > 800:
            p1Score += 1
        init()

# Prueft ob der Ball ein Paddle beruehrt hat
def checkCollide(ballRect, paddleRect):
    return (
            ballRect.x < paddleRect.x + paddleRect.w and
        ballRect.y < paddleRect.y + paddleRect.h and
        ballRect.x + ballRect.w > paddleRect.x and
        ballRect.y + ballRect.h > paddleRect.y
    )


# Ball an der Position 'ballX, ballY' zeichnen
def drawBall():
    screen.draw.filled_circle((ballX, ballY), 8, "white")
    pass

# Startet die Initialisierung des Spiels
init()
pgzrun.go()


```
