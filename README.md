# Game-codeclass Bird {
  PVector pos;
  PVector vel;
  PVector acc;
  float r = 16;

  Bird() {
    pos = new PVector(50, height/2);
    vel = new PVector(0, 0);
    acc = new PVector();
  }

  void applyForce(PVector force) {
    acc.add(force);
  }

  void update() {
    applyForce(gravity);
    pos.add(vel);
    vel.add(acc);
    vel.limit(4);
    acc.mult(0);

    if (pos.y > height) {
      pos.y = height;
      vel.mult(0);
    }
  }

  void show() {
    stroke(255);
    fill(255);
    //ellipse(pos.x, pos.y, r*2, r*2);
    image(myImg, pos.x, pos.y, r*7,r*7);
  }
}import processing.serial.*;

boolean gameOver = false;

Bird b;
int wid = 800;
int rez = 20;
int score = 0;
boolean jumping = false;
PVector gravity = new PVector(0, 0.5);
ArrayList<Pipe> pipes = new ArrayList<Pipe>();
PImage myImg;

PImage go;

void setup() {
  size(800, 800);
  b = new Bird();
  pipes.add(new Pipe());
  myImg = loadImage("image001-1.png");
  go = loadImage("gameover.jpg");
  setupSerial();
}

void draw() {
  background(0);


  if (!gameOver) {
    if (frameCount % 75 == 0) {
      pipes.add(new Pipe());
    }

    if (keyPressed) {
      PVector up = new PVector(0, -5);
      b.applyForce(up);
    }

    b.update();
    b.show();
    boolean safe = true;

    for (int i = pipes.size() - 1; i >= 0; i--) {
      Pipe p = pipes.get(i);
      p.update();

      if (p.hits(b)) {
        p.show(true);
        safe = false;
      } else {
        p.show(false);
      }

      if (p.x < -p.w) {
        pipes.remove(i);
      }
    }

    if (safe) {
      score++;
    } else {
      score -= 50;
    }

    fill(255, 0, 255);
    textSize(64);
    text(score, width/2, 50);
    score = constrain(score, 0, score);

    if (score <=0) {
      gameOver=true;
    }
  } else {class Pipe {
  float x;
  float top;
  float bottom;
  float w = 40;

  Pipe() {
    x = wid + w;
    top = random(100, height/2);
    bottom = random(100, height/2);
  }

  boolean hits(Bird b) {
    if ((b.pos.x > x) && (b.pos.x < (x + w))) {
      if ((b.pos.y < (top + b.r)) || (b.pos.y > (height - bottom - b.r))) {
        return true;
      }
    }
    return false;
  }

  void update() {
    x -= 3;
  }

  void show(boolean hit) {
    stroke(255);
    
    if (hit) {      
      fill(255, 0, 0);
    } else {
      fill(255);
    }import processing.serial.*;

Serial myPort;
int val;

float sensor1 = 0;


void setupSerial() {
  printArray(Serial.list());
  try {
    String portName = Serial.list()[2];
    myPort = new Serial(this, portName, 9600);  //115200
    println("connected to -->  " +portName);
    myPort.bufferUntil(','); //enables to split the data via a comma which is set in teh arduino code
  } 
  catch (Exception e) {
    println("no device connection found");
  }
}


void serialEvent(Serial myPort) {

  String inString = myPort.readStringUntil(',');
  // split the string into multiple strings
  // where there is a delimter":"

  // println(inString); //data line coming in

  String items[] = split(inString, ':');
  // if there was more than one string after the split
  if (items.length > 1) {
    String label = trim(items[0]);
    // remove the ',' off the end
    String val = split(items[1], ',')[0];

    // check what the label was and update the appropriate variable
    if (label.equals("S1")) {
      println("looks like sensor 1 was   "+val);
    }
    sensor1 = float(val);
    if (sensor1 > 60) {
      PVector up = new PVector(0, -5);
      b.applyForce(up);
    }
  }
}
    
    rect(x, 0, w, top); 
    rect(x, height - bottom, w, bottom);
  }
}
    background(255, 255, 255);
    image(go, 0, 0, 800, 800);
  }
}
