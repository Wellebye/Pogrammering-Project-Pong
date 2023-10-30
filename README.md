# basis

Navn: Daniel Berg Pedersen 2.P, William Egelund Ellebye 2.P
Programmeringssprog: Python
Udviklingsmiljø: Skole og hjemmearbejde
Antal tegn: 8984

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

Hvis bolden kommer i kontakt med hvilken som helst del af paddles/væggene vil deres nuværende x og y hastighedsværdi ganges med -1, hvilket ændrer deres retning uanset den forrige hastighed

if pygame.sprite.spritecollide(self,self.paddles,False):
			pygame.mixer.Sound.play(plob_sound)
			collision_paddle = pygame.sprite.spritecollide(self,self.paddles,False)[0].rect
			if abs(self.rect.right - collision_paddle.left) < 10 and self.speed_x > 0:
				self.speed_x *= -1
			if abs(self.rect.left - collision_paddle.right) < 10 and self.speed_x < 0:
				self.speed_x *= -1
			if abs(self.rect.top - collision_paddle.bottom) < 10 and self.speed_y < 0:
				self.rect.top = collision_paddle.bottom
				self.speed_y *= -1
			if abs(self.rect.bottom - collision_paddle.top) < 10 and self.speed_y > 0:
				self.rect.bottom = collision_paddle.top
				self.speed_y *= -1

Hvis bolden er blevet reset vil den blive sendt enten op eller ned, venstre eller højre ligesom i starten. Derudover vil den også spille en pointlyd uanset hvem der fik pointet

def reset_ball(self):
		self.active = False
		self.speed_x *= random.choice((-1,1))
		self.speed_y *= random.choice((-1,1))
		self.score_time = pygame.time.get_ticks()
		self.rect.center = (screen_width/2,screen_height/2)
		pygame.mixer.Sound.play(score_sound)

Når bolden er reset bliver tiden også reset som kan ses i forrige kode. Herfra opdatere koden bare indtil at current time + self.score er over 2100. Når den er det vil self.active være true, hvilket betyder at self.speed_x/y også er true, dermed bevæger bolden sig igen.

def restart_counter(self):
		current_time = pygame.time.get_ticks()
		countdown_number = 3

if current_time - self.score_time <= 700:
			countdown_number = 3
if 700 < current_time - self.score_time <= 1400:
			countdown_number = 2
if 1400 < current_time - self.score_time <= 2100:
			countdown_number = 1
if current_time - self.score_time >= 2100:
			self.active = True

Player og opponent score starter på variablen 0. Når ball bliver reset kigger koden på hvilken side bolden blev reset på. Hvis den blev reset på højre side vil modstanderen få +1 point. Hvis det modsatte sker vil den anden spillers score gå op med en. 

self.player_score = 0
self.opponent_score = 0
def reset_ball(self):
		if self.ball_group.sprite.rect.right >= screen_width:
			self.opponent_score += 1
			self.ball_group.sprite.reset_ball()
		if self.ball_group.sprite.rect.left <= 0:
			self.player_score += 1
			self.ball_group.sprite.reset_ball()

Kode for font, størrelse, farve, position for scorerne indenfor spillet. 

def draw_score(self):
		player_score = basic_font.render(str(self.player_score),True,accent_color)
		opponent_score = basic_font.render(str(self.opponent_score),True,accent_color)

player_score_rect = player_score.get_rect(midleft = (screen_width / 2 + 40,screen_height/2))
		opponent_score_rect = opponent_score.get_rect(midright = (screen_width / 2 - 40,screen_height/2))

screen.blit(player_score,player_score_rect)
		screen.blit(opponent_score,opponent_score_rect)


Hvis der trykkes på knapper, så vil der ske noget.
Hvis der trykkes på quit knappen vil systemet lukke. Derfor bliver der importet sys i starten af spillet. 
Hvis der trykkes KEYDOWN på UP, w så vil væggen bevæge sig opad så længe knappen er holdt. Hvis DOWN, s bliver holdt nede vil væggene bevæge sig ned af
Event.type KEYUP sørger for at væggenes Y koordinater ikke ændrer sig når der gives slip på knapperne.

for event in pygame.event.get():
		if event.type == pygame.QUIT:
			pygame.quit()
			sys.exit()
		if event.type == pygame.KEYDOWN:
			if event.key == pygame.K_UP:
				player.movement -= player.speed
			if event.key == pygame.K_w:
				opponent.movement -= opponent.speed
			if event.key == pygame.K_DOWN:
				player.movement += player.speed
			if event.key == pygame.K_s:
				opponent.movement += opponent.speed
		if event.type == pygame.KEYUP:
			if event.key == pygame.K_UP:
				player.movement += player.speed
			if event.key == pygame.K_w:
				opponent.movement += opponent.speed
			if event.key == pygame.K_DOWN:
				player.movement -= player.speed
			if event.key == pygame.K_s:
				opponent.movement -= opponent.speed

# Flowchart
Please check Flowchart.png

# Udviklingsprocess
Vi startede med at lave selve skærmen samt indsætte pygame. Dette gjorde vi af den simple grund af at det er nemmere at arbejde med noget som man kan se.
Herefter valgte vi at implementere baggrunden, samt de 3 vigtigste komponenter: 2 Vægge og 1 bold.
Når det var gjort og væggene og bolden stod ordenligt valgte vi at fokusere på boldens bevægelse rundt. Det gjorde vi ved konstant at ændre dens x og y koordinat og gange den med -1 når den ramte vægge. For at kunne få den til at skifte retning blev vi dog nød til at lave boundries så bolden ikke bare forsvandt. Når boundries var lavet satte vi også bolden til at bevæge retning når den ramte disse boundries.
Når alt det var gjort havde vi 2 vægge, en baggrund og en bold der bare farede rundt inde på skærmen. Herfra fokuserede vi på væggenes bevægelse. Dette gjorde vi ved at ændre deres Y koordinat når du trykker på W,S, UP og DOWN. Herefter skulle vi igen implementere boundries så væggene heller ikke kunne flygte væk fra skærmen.
Når alt kunne bevæge sig og ikke ud fra skærmen lavede vi collision mellem bold og væg, så når bolden ramte væggen ville den også *= -1 ligesom med murene af skærmen. Herfra blev vi nødt til på x aksen at detektere når bolden kom udenfor skærmens pixels eller lig med skærmens pixels. Hermed hvis bolden nåede boldX >= 0 eller boldX =< 1250. Hvis den gjorde det ville vi skulle reset bolden tilbage til startpunkt. Udover det ville reset også fungere som pointtæller. Når den resat bolden fandt vi ud af om bolden var over 1250 eller under 0 og gav modstander siden 1 point som vi holdte styr på via en tæller. 
Nu havde vi det meste af spillet bortset fra countdownen i starten, hvilket var det sidste vi lavede. timeren gjorde boldens X- og YChange inaktiv i de første 3 sekunder af spillet, derefter blev de igen aktive og bolden ville bevæge sig igen.

Uheldigvis kan vi ikke finde ud af hvor de gamle commits er blevet af. Branchen: færdig spil (som ikke var det færdige spil) har en fil navngivet ok som har en tidlig version af koden, men er det eneste af den kode vi har tilbage
