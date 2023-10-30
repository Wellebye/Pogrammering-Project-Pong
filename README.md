# basis

Navn: Daniel Berg Pedersen 2.P, William Egelund Ellebye 2.P
Programmeringssprog: Python
Antal tegn:

Spillet er det simple spil pong. Målet er at man skal være to spillere der kæmper mod hinanden om at blokere bolden der kommer mod en. Hvis man ikke sender bolden tilbage og bolden rammer kanten på ens egen side, vil modstanderen få et point.
Skærmlayoutet har bredere x koordinat på 1250 og en y koordinat på 800. For at bevæge væggene på hver side kan man bruge W,S for at gå op og ned med væggen på venstre side. Piletasterne op og ned gør det samme for væggen på højre side. Hvis væggene kommer i kontakt med den flyvende bold bliver den sendt i den modsatte retning, tilbage til modstanderen. 

Spillet handler om bolden og de to vægge. Når spillet startes går der 3 sekunder inden bolden bevæger sig enten til venstre eller højre, op eller nedad. Herfra vil bolden fortsætte med at ændre y og x værdi indtil den rammer toppen eller bunden af skærmen. Hvis den rammer toppen eller bunden vil bolden gå i modsat retning. Væggene har samme effekt bortset fra at de styres af spillerne. Hvis bolden rammer en væg vil x værdien af bolden ændres og flyve modsatte vej. Hvis bolden kommer i kontakt med de ydre vægge mod venstre og højre vil den ikke ændre x værdi, men spillet vil detektere at der er kontakt og bolden vil blive flyttet tilbage til starter positionen og den modsatte sides spiller vil få deres tidligere pointværdi + 1. 

# kode

Kodeblok for selve spillet. Screenwidth (X) er sat til 1250 pixels og screenheight(Y) er sat til 800 pixels. Background har loadet png image space.png som ændrer baggrunden af spillet. Pygame.display laver variablen screen sammen med screenwidth og screenheight værdierne. Pygame.setcaption er til spillets caption som er pong.

screen_width = 1250
screen_height = 800
background = pygame.image.load('space.png')
screen = pygame.display.set_mode((screen_width,screen_height))
pygame.display.set_caption('Pong')



Koden udputter hvilken retning som bolden vil flyve ved starten af spillet. Ved brug af random -1,1 ganget med boldens speed (selv.speed.y/x) vil dens hastighed enten være negativ eller positiv, hvilket vil ændre hvilken retning bolden vil bevæge sig.
class Ball(Block):
	def __init__(self,path,x_pos,y_pos,speed_x,speed_y,paddles):
		super().__init__(path,x_pos,y_pos)
		self.speed_x = speed_x * random.choice((-1,1))
		self.speed_y = speed_y * random.choice((-1,1))



Hvis bolden er i bevægelse i y og x værdi eller har collision vil bolden fortsat bevæge sig. Hvis ikke, vil bolden genstarte og sættest tilbage til startposition.
  def update(self):
		if self.active:
			self.rect.x += self.speed_x
			self.rect.y += self.speed_y
			self.collisions()
		else:
			self.restart_counter()
