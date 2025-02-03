# JavaScript-with-P5.js

// Made using p5play

/* The objective of the game is simple: Click on either one of the taps to achieve the 'Vessel Capacity' as stated on the screen. Fill it properly - Proceed to next level. Overfill it - Retry. On successful completion at the third level, game ends. */

/*How p5play is utilised here: Sprite objects and handling for taps, collision detection on tap pressing, efficient water droplet/overflow animation, sprite removal (off-screen)*/

let level = 1;
let vesselCapacity = 200; // Initial capacity for level 1
let currentWater = 0;
let taps = [];
let tapMeasurements = [];
let timer = 0; // Timer for the final level
let gameOver = false;

// Audio variables
let waterSound, overflowSound, winSound;

// Waterfall and overflow animations
let waterSprites = [];
let overflowSprites = [];

// Preload sounds
function preload() {
  waterSound = loadSound("water.wav"); // Water droplet sound
  overflowSound = loadSound("overflow.mp3"); // Overflow sound
  winSound = loadSound("win.wav"); // Level-complete win sound
}

function setup() {
  createCanvas(600, 400);
  createTaps([50, 100, 150]);
}

function draw() {
  background(200);
  drawVessel();

  // Display current water level and instructions
  fill(0);
  textSize(16);
  text(`Level: ${level}`, 20, 30);
  text(`Vessel Capacity: ${vesselCapacity} units`, 20, 50);
  text(`Current Water: ${currentWater} units`, 20, 70);

  // Timer for the last level
  if (level === 3) {
    timer += deltaTime / 1000; // Track time in seconds
    text(`Time: ${timer.toFixed(1)} seconds`, 20, 90);
    if (timer > 30) {
      loseGame("Time's up!");
    }
  }

  // Taps interaction
  for (let i = 0; i < taps.length; i++) {
    taps[i].draw();

    // Highlight the tap being pressed
    if (taps[i].mouse.presses()) {
      taps[i].color = "red"; // Pressed state color
      currentWater += tapMeasurements[i];
      playWaterSound();
      createWaterAnimation(taps[i].x, taps[i].y); // Water animation
      checkVesselOverflow();
    } else {
      taps[i].color = "#64C8FF"; // Default tap color
    }

    // Display tap quantity above each tap
    fill(0);
    textSize(20);
    text(`${tapMeasurements[i]}ml`, taps[i].x - 20, taps[i].y - 20);
  }

  // Water Animations
  for (let sprite of waterSprites) {
    sprite.y += 2; // Water falls
    sprite.draw();
    if (sprite.y > height) sprite.remove(); // Remove out-of-screen sprites. The .remove() function ensures that water droplets (sprites) falling out of the visible canvas are deleted.
  }

  // Overflow animations
  for (let sprite of overflowSprites) {
    sprite.y -= 2; // Overflow water rises
    sprite.draw();
    if (sprite.y < 0) sprite.remove(); // Remove out-of-screen sprites
  }
}

// taps with specific measurements
function createTaps(measurements) {
  taps = [];
  tapMeasurements = measurements;
  for (let i = 0; i < measurements.length; i++) {
    let tap = new Sprite(100 + i * 150, 300, 80, 40);
    tap.color = "#64C8FF"; // Initial tap color
    taps.push(tap);
  }
}

// Water Vessel
function drawVessel() {
  fill(255);
  rect(250, 150, 100, 200);
  fill(0, 0, 255, 150); // Water color
  rect(
    250,
    350 - map(currentWater, 0, vesselCapacity, 0, 200),
    100,
    map(currentWater, 0, vesselCapacity, 0, 200)
  ); // Maps the water level to a visual height, making sure that as water level increases, it fills up the rectangle representing the vessel proportionally.
}

// Check if the vessel overflows or is filled
function checkVesselOverflow() {
  if (currentWater > vesselCapacity || gameOver) {
    loseGame("Overflow! Try again.");
    playOverflowSound();
    createOverflowAnimation();
  } else if (currentWater === vesselCapacity) {
    levelUp();
    playWinSound();
  }
}

// Water Overflow animation
function createOverflowAnimation() {
  for (let i = 0; i < 20; i++) {
    let sprite = new Sprite(random(250, 350), random(150, 350), 10, 10);
    sprite.color = "#0000FFAA"; // Semi-transparent blue
    overflowSprites.push(sprite);
  }
}

// Losing the game
function loseGame(message) {
  alert(message);
  currentWater = 0;
  if (level === 3) timer = 0;
  overflowSprites = []; // Resets Overflow Animation
}

// Leveling up
function levelUp() {
  alert("Level Complete! Moving to the next level.");
  level++;
  currentWater = 0;

  if (level === 2) {
    vesselCapacity = 300;
    createTaps([50, 100, 150, 200, 250]);
  } else if (level === 3) {
    vesselCapacity = 400;
    timer = 0;
    createTaps([50, 100, 150, 200, 250]);
  } else {
    alert("Congratulations! You completed all levels!");
    noLoop(); // Stop the game
  }
}

// Create water animation
function createWaterAnimation(x, y) {
  let sprite = new Sprite(x, y, 10, 20);
  sprite.color = "#0000FFAA"; // Semi-transparent blue
  waterSprites.push(sprite);
}

function getCurrentWaterLevel() {
  return currentWater;
}

// Play water sound
function playWaterSound() {
  if (waterSound && !waterSound.isPlaying()) {
    waterSound.play();
  }
}

// Play overflow sound
function playOverflowSound() {
  if (overflowSound) {
    overflowSound.play();
  }
}

// Play win sound
function playWinSound() {
  if (winSound) {
    winSound.play();
  }
}

//Learning Links:

/* Deltatime: https://p5js.org/reference/p5/deltaTime/

 https://www.youtube.com/watch?v=Ei6AGGRsT_E&ab_channel=flanniganable */

/* p5play library: https://p5play.org/learn/

https://www.youtube.com/watch?v=oM8inLUPrUs&t=321s&ab_channel=GABRIELWALTERS

https://www.youtube.com/watch?v=ihkjVACp6pU&list=PLvqAGa7mJm0XgzljScjXUsbOLshmIQ7-S&index=2&ab_channel=MatthewBardin

p5play.remove: https://p5play.org/learn/sprite.html?page=6*/

// Loadsound: https://p5js.org/reference/p5/loadSound/

/*Template literals versus concatenation: https://www.w3schools.com/js/js_string_templates.asp

https://www.educative.io/answers/string-concatenation-using-p-operator-vs-template-literals-in-js */

// === comparison operator: https://www.w3schools.com/js/js_comparisons.asp
