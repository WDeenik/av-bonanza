let c;

let bgHue = 0;
let bgSaturation = 0;
let bgBrightness = 0;
let bgOpacity = 0;

function preload() {
	loadShaders();
}

function setup() {
	// Setup
	createCanvas(windowWidth, windowHeight, WEBGL);
	c = createGraphics(windowWidth, windowHeight);
	
	setupShaders();
	
	// Sync color mode with MIDI values
	c.colorMode(HSB, 127);

	// Default draw style
	c.fill(255);
	c.strokeWeight(0);
	
	// Center all draws
	c.translate(width / 2, height / 2);
}

function draw() {
	// Fade trail
	c.background(bgHue, bgSaturation, bgBrightness, bgOpacity);

	// Generated chasing dots
	for(let i = 1; i <= 12; i++) {
		drawStep(i / 4, i / 4, 3);
	}
	
	applyShaders();
}


//**************************************
// ANIMATION SHIZZLE
//**************************************

function drawStep(xSpeed, ySpeed, pulseSpeed) {
	let oscX = sin(frameCount * 0.01 * xSpeed) * height * .4;
	let oscY = cos(frameCount * 0.01 * ySpeed) * height * .4;
	let radius = positiveModulate(sin(frameCount * 0.01 * pulseSpeed)) * 10 + 10;
	
	c.ellipse(oscX, oscY, radius, radius);
}

function positiveModulate(value) {
	return (value + 1) / 2;
}

//**************************************
// SHADER SHIZZLE
//**************************************

let rgbShader, scanLineShader;
let pass1;
let xOffset = 0.001;
let yOffset = 0;
let xLineWidth = 0.0008;
let xLineOffset = 0.002;
let yLineWidth = 0.0015;
let yLineOffset = 0.01;

function loadShaders() {
	rgbShader = loadShader('/data/shader.vert', '/data/rgbSplit.frag');
	scanLineShader = loadShader('/data/shader.vert', '/data/scanlines.frag');
}

function setupShaders() {
	pass1 = createGraphics(windowWidth, windowHeight, WEBGL);
	
	pass1.noStroke();
	noStroke();
}

function applyShaders() {
	scanLineShader.setUniform('tex0', c);
	scanLineShader.setUniform('xLineWidth', xLineWidth);
	scanLineShader.setUniform('yLineWidth', yLineWidth);
	scanLineShader.setUniform('xLineOffset', xLineOffset);
	scanLineShader.setUniform('yLineOffset', yLineOffset);
	pass1.shader(scanLineShader);
	pass1.rect(0, 0, width, height);
	
	rgbShader.setUniform('tex0', pass1);
	rgbShader.setUniform('xOffset', xOffset);
	rgbShader.setUniform('yOffset', yOffset);
	
	shader(rgbShader);
	rect(0, 0, width, height);
}

//**************************************
// MIDI sharing SHIZZLE
//**************************************

// My stuff

const midiSlider1 = 0;
const midiSlider2 = 1;
const midiSlider3 = 2;
const midiSlider4 = 3;
const midiKnob1 = 16;
const midiKnob2 = 17;
const midiKnob3 = 18;
const midiKnob4 = 19;
const midiKnob5 = 20;
const midiKnob6 = 21;
const midiKnob7 = 22;
const midiKnob8 = 23;

function mapMidiToValue(key, value) {
	switch(key) {
		case midiKnob1:
			xOffset = value / 127 / 100;
			break;
		case midiKnob2:
			yOffset = value / 127 / 100;
			break;
		case midiSlider1:
			bgHue = value;
			break;
		case midiSlider2:
			bgSaturation = value;
			break;
		case midiSlider3:
			bgBrightness = value;
			break;
		case midiSlider4:
			bgOpacity = value;
			break;
	}
}

// Generated

function parseData(obj) {
	// print(obj) // debug incoming message

	// parse only 'midi' data
	if(obj.type && obj.type == 'midi') {
		let mm = obj; // midimessage
		if(mm.note !== undefined) {
			switch (mm.note.type) {
				case 'noteon':
					noteOn(mm.note);
					break;
				case 'noteoff':
					noteOff(mm.note);
					break;
			}
		} else if(mm.pitch !== undefined) {
			pitchBend(mm.pitch);
		} else if(mm.control !== undefined) {
			controlChange(mm.control);
		}

		// optionally listen to midiMessages from others on your local gear!
		getData(obj); // ignored by those without syncData active
	} else if(obj.type && obj.type == 'latency') {
		getData(obj);
	}
}

function noteOn(note) {
	// use note.type, .channel, .name, .number, .octave, .velocity
	let x = map(note.number, 0, 128, 0, width);
	let h = map(note.velocity, 0, 128, 0, height);
	c.noStroke();
	c.fill(note.velocity * 2);
	c.rectMode(CENTER);
	c.rect(x, height / 2, width / 128, h);
}

function noteOff(note) {
	// use note.type, .channel, .name, .number, .octave, .velocity
}

function pitchBend(pitch) {
	// use pitch.type, .channel, .value
	console.log(pitch.value);
}

function controlChange(control) {
	// use control.type, .channel, .controllerNumber, .controllerName, .value
	mapMidiToValue(control.controllerNumber, control.value);
}

function midiToFreq(noteNumber) {
	return 440 * Math.pow(2, (noteNumber - 69) / 12);
}
