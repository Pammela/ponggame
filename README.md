# ponggame
// press 'enter' 
// win or lost (CpuScore >=LastLevel || PlayerScore >=LastLevel) the game decided who won)
// at the end of this game it shows who wins and loses
//right paddle move with the mouse (player)



float speed = 0.3;
float CpuPaddleSpeed = 7;
int LastLevel = 5;
Paddle Paddle;
Paddle CpuPaddle;
Pluck  pluck;
boolean keypress = false;
boolean endgame = false;
int PlayerScore = 0;
int CpuScore = 0;

float t;
static final int NUM_LINES=10;
float xx1(float t){
  return sin(t/5) * 100 + sin (t/10)*10;

}
float yy1(float t){
  return cos(t/15) *200 + sin(t/5)*70;
}
float xx2(float t){
  return sin(t/10) * 2500 + sin (t/10)*2 + cos(t)*100;
}
float yy2(float t){
  return cos(t/50) *100 + cos (t/15) *10;
}
 
void setup() {
  size(550, 400);
  smooth();
  CpuPaddle = new Paddle(20, 200);
  Paddle = new Paddle(530, 200);
  pluck = new Pluck();
}
 
void draw() {
  //table
  background(1);
  stroke(15);
  strokeWeight (7);
  line(width/2, 0, width/2, height);
 
  // score
  fill(255,250,70);
  textSize(25);
  text(PlayerScore, width/2+20, 50);
  fill(255, 250,70);
  text(CpuScore, width/2-50, 50);
 
  
  Paddle.display();
  CpuPaddle.display();
  pluck.display();
  if (endgame) {   // game ends
    textSize(15);
    fill(random(255),random(300),0);
    text("PRESS ENTER", width/2+width/4-80, height/2+135);
    text("START", width/2+width/4-80, height/2+160);
    textSize(30);
    fill(random(200),random(250),200);
    text("GAME OVER", width/2-width/4 +200, height/2-80);
    whowins();
  }
  else {  // game not ends
    if (keypress == true) {
      pluck.move(speed);
      Paddle.moveY(mouseY);
      CpuPaddle.cpu(pluck);
      CpuPaddle.Right(pluck);
        
       
      Paddle.Left(pluck);
      pluck.checkGoal();
    }
    else {
      textSize(20);
      fill(random(255), 0,random(255));
      if (CpuScore >=LastLevel || PlayerScore >=LastLevel) {
        endgame = true;
      }
      else {
        text("PRESS ENTER", width/4+width/4-60, height/5+25);
        text("START", width/4+width/4-30, height/5+50);
      }
    }
  }
  
  stroke (random(255),random(255), 255);
  strokeWeight (4);
  
  translate (width/2, height/2);
  
  for(int i=0; i< NUM_LINES; i++){
    line(xx1(t +i), yy1(t+i),xx2(t+i), yy2(t+i));
  }
  t+= 0.5;
  
}
 
void whowins() {  //display on screen who wins and who loses.
  if (CpuScore >= LastLevel || PlayerScore >= LastLevel) {
    textSize(25);
 
    if (CpuScore > PlayerScore) {
      fill(255, 205, 0, 180);
      text("CPU WINS", width/4-80, 50);
      fill(255, 0, 0, 180);
      text("PLAYER LOSES", width/2+80, 50);
    }
    else {
      fill(255, 0, 0, 180);
      text("CPU LOSES", width/4-80, 50);
      fill(0, 255, 0, 180);
      text("PLAYER WINS", width/2+80, 50);
    }
  }
}
 
//start & pause
void keyPressed() {
  speed = 8;
  if ( key == ENTER) {
    if (endgame) {
      endgame = false;
      CpuScore = 0;
      PlayerScore = 0;
    }
    else {
      keypress = !keypress;
    }
  }
}

class Paddle {
  int x;
  int y;
  int w = 10;
  int h = 50;
  Paddle(int tx, int ty) {
    x = tx;
    y = ty;
  } 
 
  void display() {
    rectMode(CENTER);
    fill(255,25,70);
    noStroke();
    rect(x, y, w, h);
  }
 
  void moveY(int ty) {
    y = ty;
    //boundries
    if (y > height-h/2) {
      y = height-h/2;
    }
    else if ( y < 0 + h/2) {
      y = h/2;
    }
  }
 
  void cpu(Pluck p) {
    if (p.x <= width/2) {
      if (y < p.y-p.r*2) {
        y +=CpuPaddleSpeed;
      }
      else if (y > p.y +p.r*2) {
        y -= CpuPaddleSpeed;
      }
    }
    else {
      if (y < height/2) {
        y+=CpuPaddleSpeed;
        ;
      }
      if ( y > height/2) {
        y-=CpuPaddleSpeed;
      }
    }
  }
 
  void Left(Pluck p) {
    if (p.x > x-w/2 && (p.y > y - h/2 && p.y < y + h/2)) {
      p.directionX = -p.directionX;
      speed+=1;
      println(speed);
    }
  }
 
  void Right(Pluck p) {
    if (p.x < x+w/2 && (p.y > y - h/2 && p.y < y + h/2)) {
      p.directionX = -p.directionX;
      speed+=1;
     
    }
  }
}
 
class Pluck {
  int x = width/2;
  int y = height/2;
  int r = 6;  
  float speed;
  int directionX = 1;
  int directionY = 1;
  Pluck() {
  } 
 
  void display() {
    noStroke();
    fill(255,255,70);
    ellipse(x, y, r*2, r*2);
  }
 
  void move(float tspeed) {
    speed = tspeed;
    x += speed * directionX;
    y += speed * directionY;
    
    if ( y < r) {
      y = r;
      directionY = -directionY;
    }
    else if ( y > height-r) {
      y = height-r;
      directionY = -directionY;
    }
  }
 
 
  void checkGoal() {
    if ( x > width+r) {
      x = width/2;
      y = height/2;
      keypress = false;
      CpuScore = CpuScore+1;
      directionX = -directionX;
      directionY = -directionY;
    }
    else if (x < -r) {
      x = width/2;
      y = height/2;
      keypress = false;
      PlayerScore = PlayerScore+1;
      directionX = -directionX;
      directionY = -directionY;
    }
  }
}
