# Portfolio
import pygame
from pygame.locals import *
import sys
import time
import random

#Typing test themed by the 48 laws of power.

##About the book:
##
##An outzstanding book that will no doubt remain a classic for a long time.
##48 Laws of Power details the laws for attaining power in life, business,
##and more, and gives historical examples of each law in practice, as well as
##examples of those who do not respect these laws.
##
##Feeling powerless is a miserable experience. If given the choice,
##everyone would opt for more rather than less power. Yet, to be so overt in
##attempts to gain power is frowned upon. To attain power, you need to be subtle,
##cunning, and democratic yet devious. Consequently, in his controversial book,
##“The 48 Laws of Power,” best-selling author Robert Greene argues that if
##you manage to seduce, charm, and deceive your opponents, you will attain the
##ultimate power.
##
##To put it into simpler terms, the 48 laws of power is about manipulation, and
##is a book written by a sociopath for sociopaths.
##
##However, knowing about the 48 laws of power can be useful in your everyday
##life. It is possible to apply them without being psychotic. 
##
##While you, may not enjoy manipulating the people around you, many others cannot
##say the same for themselves. The 48 laws of power can also be a very useful
##self defence weapon and could be used as a "red flag" list when around people
##you are getting to know. If you can observe them applying any of these laws,
##you will know that it would be a good idea to stop talking to them.
##
##To learn more about the 48 laws of power, use the link below to read more about
##each rule in better detail.
##
##https://www.oberlo.ca/blog/48-laws-of-power-robert-greene-summary#:~:text=
## Consequently%2C%20in%20his%20controversial%20book,and%20person%20you%20will%
## 20become.
    
class Game:
   
    def __init__(self):
        self.w=800
        self.h=500
        self.reset=True
        self.active = False
        self.input_text=''
        self.word = ''
        self.time_start = 0
        self.total_time = 0
        self.accuracy = '0%'
        self.results = 'Time:0 Accuracy:0 % Wpm:0 '
        self.wpm = 0
        self.end = False  
        self.HEAD_C = (255,213,102)
        self.TEXT_C = (240,240,240)
        self.RESULT_C = (255,70,70)
        
       
        pygame.init()
        self.open_img = pygame.image.load('type-speed-open.png')
        self.open_img = pygame.transform.scale(self.open_img, (self.w,self.h))


        self.bg = pygame.image.load('background.jpg')
        self.bg = pygame.transform.scale(self.bg, (500,750))

        self.screen = pygame.display.set_mode((self.w,self.h))
        pygame.display.set_caption('Type Speed test')
       
        
    def draw_text(self, screen, msg, y ,fsize, color):
        font = pygame.font.Font(None, fsize)
        text = font.render(msg, 1,color)
        text_rect = text.get_rect(center=(self.w/2, y))
        screen.blit(text, text_rect)
        pygame.display.update()   
        
    def get_sentence(self):
        f = open('sentences.txt').read()
        sentences = f.split('\n')
        sentence = random.choice(sentences)
        return sentence

    def show_results(self, screen):
        if(not self.end):
            
            #this part calculates the time
            self.total_time = time.time() - self.time_start
               
            #calculates the accuracy
            count = 0
            for i,c in enumerate(self.word):
                try:
                    if self.input_text[i] == c:
                        count += 1
                except:
                    pass
            self.accuracy = count/len(self.word)*100
           
            #calculates the words per minute
            self.wpm = len(self.input_text)*60/(5*self.total_time)
            self.end = True
            print(self.total_time)
                
            self.results = 'Time:'+str(round(self.total_time)) +" secs   Accuracy:"+ str(round(self.accuracy)) + "%" + '   Wpm: ' + str(round(self.wpm))

            #resets

            self.draw_text(screen,"Reset", self.h - 70, 26, (250, 2, 2))
            
            print(self.results)
            pygame.display.update()

    def run(self):
        self.reset_game()
    
       
        self.running=True
        while(self.running):
            clock = pygame.time.Clock()
            self.screen.fill((0,0,0), (50,250,650,50))
            pygame.draw.rect(self.screen,self.HEAD_C, (50,250,650,50), 2)
            
            #updates the text of input put in another file
            self.draw_text(self.screen, self.input_text, 274, 26,(250,250,250))
            pygame.display.update()
            for event in pygame.event.get():
                if event.type == QUIT:
                    self.running = False
                    sys.exit()
                elif event.type == pygame.MOUSEBUTTONUP:
                    x,y = pygame.mouse.get_pos()
                    
                    #position of the input box
                    if(x>=50 and x<=650 and y>=250 and y<=300):
                        self.active = True
                        self.input_text = ''
                        self.time_start = time.time()
                        
                     #position of reset box (where to click)
                    if(x>=310 and x<=510 and y>=390 and self.end):
                        self.reset_game()
                        x,y = pygame.mouse.get_pos()
         
                        
                elif event.type == pygame.KEYDOWN:
                    if self.active and not self.end:
                        if event.key == pygame.K_RETURN:
                            print(self.input_text)
                            self.show_results(self.screen)
                            print(self.results)
                            self.draw_text(self.screen, self.results,350, 28, self.RESULT_C)  
                            self.end = True
                            
                        elif event.key == pygame.K_BACKSPACE:
                            self.input_text = self.input_text[:-1]
                        else:
                            try:
                                self.input_text += event.unicode
                            except:
                                pass
            
            pygame.display.update()
             
                
        clock.tick(60)

    def reset_game(self):
        self.screen.blit(self.open_img, (0,0))

        pygame.display.update()
        time.sleep(1)
        
        self.reset=False
        self.end = False
        self.active=False

        self.input_text=''
        self.word = ''
        self.time_start = 0
        self.total_time = 0
        self.wpm = 0

        #gets random sentence 
        self.word = self.get_sentence()
        if (not self.word): self.reset_game()
        #drawing heading
        
        
        msg = "Typing Speed Test"
        self.draw_text(self.screen, msg,80, 80,self.HEAD_C)
        
        # raws the rectangle for input box
        pygame.draw.rect(self.screen,(247, 2, 2), (50,250,650,50), 2)

        #draws the sentence string
        self.draw_text(self.screen, self.word,200, 28,self.TEXT_C)
        
        pygame.display.update()



Game().run()
