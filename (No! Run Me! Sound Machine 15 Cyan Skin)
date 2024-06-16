import os
import subprocess
import threading
import tkinter as tk
from tkinter import ttk
from math import cos, sin, radians

# Define the main application class
class SoundMachineApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Sound Machine")
        self.root.configure(bg='#006060')  # Set background to brown

        self.current_sound = None
        self.chuck_process = None
        self.volume = 0.5
        self.timer = None

        # Create a canvas for circular layout
        self.canvas = tk.Canvas(self.root, width=800, height=600, bg='#006060', highlightthickness=0)
        self.canvas.pack(pady=20)

        # Create sound buttons
        self.buttons = {
            "Ocean Waves": tk.Button(self.root, text="Ocean Waves", command=lambda: self.select_sound("Ocean Waves"), width=15, height=2, bg='lightgrey'),
            "Spring Rain": tk.Button(self.root, text="Spring Rain", command=lambda: self.select_sound("Spring Rain"), width=15, height=2, bg='lightgrey'),
            "Mountain Stream": tk.Button(self.root, text="Mountain Stream", command=lambda: self.select_sound("Mountain Stream"), width=15, height=2, bg='lightgrey'),
            "Heartbeat": tk.Button(self.root, text="Heartbeat", command=lambda: self.select_sound("Heartbeat"), width=15, height=2, bg='lightgrey'),
            "Volcano Lava": tk.Button(self.root, text="Volcano Lava", command=lambda: self.select_sound("Volcano Lava"), width=15, height=2, bg='lightgrey'),
            "Summer Night": tk.Button(self.root, text="Summer Night", command=lambda: self.select_sound("Summer Night"), width=15, height=2, bg='lightgrey'),
            "Jungle Noise": tk.Button(self.root, text="Jungle Noise", command=lambda: self.select_sound("Jungle Noise"), width=15, height=2, bg='lightgrey'),
            "Inside Volcano": tk.Button(self.root, text="Inside Volcano", command=lambda: self.select_sound("Inside Volcano"), width=15, height=2, bg='lightgrey')
        }

        # Place buttons on the canvas
        positions = {
            "Ocean Waves": (100, 100),
            "Spring Rain": (300, 100),
            "Mountain Stream": (500, 100),
            "Heartbeat": (700, 100),
            "Volcano Lava": (100, 300),
            "Summer Night": (300, 300),
            "Jungle Noise": (500, 300),
            "Inside Volcano": (700, 300)
        }

        for sound, button in self.buttons.items():
            x, y = positions[sound]
            self.canvas.create_window(x, y, window=button)

        # Create the central button
        self.central_button = tk.Button(self.root, text="OFF/RESUME", command=self.toggle_sound, width=15, height=2, bg='grey')
        self.canvas.create_window(400, 450, window=self.central_button)

        # Volume and timer controls
        self.controls_frame = tk.Frame(self.root, bg='#006060')
        self.controls_frame.pack(pady=20)
        
        self.volume_label = tk.Label(self.controls_frame, text="Volume", bg='#006060', fg='white')
        self.volume_label.pack()
        
        self.volume_slider = tk.Scale(self.controls_frame, from_=0, to=100, orient='horizontal', bg='#006060', fg='white', command=self.update_volume)
        self.volume_slider.set(50)
        self.volume_slider.pack()
        
        self.timer_label = tk.Label(self.controls_frame, text="Timer (minutes)", bg='#006060', fg='white')
        self.timer_label.pack()
        
        self.timer_slider = tk.Scale(self.controls_frame, from_=0, to=60, orient='horizontal', bg='#006060', fg='white')
        self.timer_slider.set(15)
        self.timer_slider.pack()

    def update_volume(self, event=None):
        self.volume = self.volume_slider.get() / 100.0
        if self.chuck_process:
            self.restart_sound()

    def select_sound(self, sound):
        self.current_sound = sound
        for button in self.buttons.values():
            button.configure(bg='lightgrey')
        self.buttons[sound].configure(bg='blue')

    def toggle_sound(self):
        if self.chuck_process:
            self.stop_sound()
        else:
            self.play_sound()

    def play_sound(self):
        if self.current_sound:
            self.start_chuck()
        if self.timer:
            self.root.after_cancel(self.timer)
        timer_minutes = self.timer_slider.get()
        if timer_minutes > 0:
            self.timer = self.root.after(timer_minutes * 60000, self.stop_sound)

    def stop_sound(self):
        if self.chuck_process:
            self.chuck_process.terminate()
            self.chuck_process = None
        self.central_button.configure(bg='grey')

    def restart_sound(self):
        self.stop_sound()
        self.play_sound()

    def start_chuck(self):
        if self.current_sound == "Ocean Waves":
            chuck_script = f"""
                // Ocean waves sound generator
                Gain g => dac;

                // Noise generator
                Noise n => LPF f => g;
                f.freq(500.0);
                f.Q(1.0);

                // Waves modulation
                SinOsc mod1 => blackhole;
                SinOsc mod2 => blackhole;
                mod1.freq(0.2);
                mod2.freq(0.1);

                // Set overall gain
                g.gain({self.volume});

                fun float mod_amplitude() {{
                    return 0.5 + 0.4 * mod1.last() + 0.2 * mod2.last();
                }}

                // Infinite loop to keep the sound playing
                while (true) {{
                    n.gain(mod_amplitude());
                    1::ms => now;
                }}
            """
        elif self.current_sound == "Spring Rain":
            chuck_script = f"""
                // Spring rain with big raindrops and occasional thunder

                // Gain control
                Gain g => dac;

                // Noise generator for continuous rain
                Noise rainNoise => HPF hpfRain => LPF lpfRain => g;
                hpfRain.freq(500.0);
                lpfRain.freq(2000.0);

                // Noise generator for big raindrops
                Noise bigRaindropNoise => BPF bpfBig => g;
                bpfBig.freq(800.0);
                bpfBig.Q(5.0);

                // Thunder sound
                Phasor thunderOsc => ADSR envThunder => Gain thunderDist => JCRev rev => Gain thunderGain => HPF hpfThunder => LPF lpfThunder => g;
                thunderOsc.freq(50.0);
                envThunder.set(0.41, 0.5, 0.2, 0.5); // Attack, Decay, Sustain, Release
                envThunder.keyOff(); // Start with the envelope off
                thunderOsc => envThunder => thunderDist => thunderGain => hpfThunder => lpfThunder => rev;
                hpfThunder.freq(8000.0); // High-pass filter to remove low-end rumble
                rev.mix(5.2);

                // Distortion parameters
                thunderDist.gain(20.0);

                // Thunder gain control
                0.01 => float thunderGainControl; // Reduced initial thunder gain value

                // Modulation for continuous rain
                SinOsc modRain => blackhole;
                modRain.freq(0.1);
                modRain.gain(0.02);

                // Set overall gain
                g.gain({self.volume});

                // Function to convert dB to linear gain
                fun float dbToGain(float db) {{
                    return Math.pow(10, db / 20.0);
                }}

                // Infinite loop to keep the sound playing
                while (true) {{
                    // Generate continuous rain sound
                    rainNoise.gain(0.1 + modRain.last() * 0.05);
                    100::ms => now;
                    
                    // Occasionally generate big raindrop sound
                    if (Math.random2(0, 100) < 10) {{
                        bigRaindropNoise.gain(0.5);
                        50::ms => now;
                        bigRaindropNoise.gain(0.0);
                    }}
                    
                    // Occasionally generate thunder sound
                    if (Math.random2(0, 50) < 1) {{ // Increased chance for testing
                        // Randomize thunder parameters
                        hpfThunder.freq(Math.random2f(1000.0, 8000.0));
                        lpfThunder.freq(Math.random2f(330.0, 400.0));
                        thunderGain.gain(thunderGainControl * dbToGain(Math.random2f(-30.0, -20.0))); // Scaled thunder gain
                        
                        envThunder.keyOn();
                        2::second => now; // Duration of the thunder
                        envThunder.keyOff();
                        3::second => now; // Wait for the envelope to release
                    }}
                }}
            """
        elif self.current_sound == "Mountain Stream":
            chuck_script = f"""
                // Mountain Stream Effect

                // Gain control
                Gain g => dac;
                g.gain({self.volume}); // Adjust overall gain

                // Mountain stream sounds
                Noise mountainStream => LPF lpfStream => g;
                mountainStream.gain(0.3); // Adjust initial gain
                lpfStream.freq(200.0); // Low-pass filter to soften high frequencies

                // Infinite loop to simulate mountain stream
                while (true) {{
                    mountainStream.gain(0.3 + 0.1 * Math.random2f(-0.5, 0.5)); // Adjust gain slightly

                    100::ms => now; // Adjust timing for natural feel
                }}
        """

        elif self.current_sound == "Heartbeat":
            chuck_script = f"""
                // Heartbeat sound

                Gain g => dac;
                g.gain({self.volume}); // Set overall gain

                SinOsc s => g;

                // Heartbeat function
                fun void heartbeat() {{
                    while (true) {{
                        50 => s.freq;
                        0.5 => s.gain;
                        0.1::second => now;
                        0 => s.gain;
                        0.4::second => now;
                        50 => s.freq;
                        0.5 => s.gain;
                        0.1::second => now;
                        0 => s.gain;
                        1.3::second => now;
                    }}
                }}

                // Spork the heartbeat function
                spork ~ heartbeat();

                // Infinite loop to keep the script running
                while (true) {{
                    1::second => now;
                }}
            """

        elif self.current_sound == "Volcano Lava":
            chuck_script = f"""
                // Volcano lava erupting sound

                // Gain control
                Gain g => dac;

                // Noise generator for continuous rumble
                Noise rumbleNoise => HPF hpfRumble => LPF lpfRumble => g;
                hpfRumble.freq(20.0); // High-pass to remove ultra-low frequencies
                lpfRumble.freq(200.0); // Low-pass to focus on the low rumble

                // Noise generator for crackle
                Noise crackleNoise => BPF bpfCrackle => g;
                bpfCrackle.freq(1000.0);
                bpfCrackle.Q(5.0);

                // Modulation for continuous rumble
                SinOsc modRumble => blackhole;
                modRumble.freq(0.1);
                modRumble.gain(0.02);

                // Set overall gain
                g.gain({self.volume});

                // Function to simulate low bit depth
                fun float lowBitDepth(float input, int bits) {{
                    return Math.round(input * bits) / bits;
                }}

                // Infinite loop to keep the sound playing
                while (true) {{
                    // Generate continuous rumble sound
                    rumbleNoise.gain(0.2 + modRumble.last() * 0.1);
                    
                    // Generate crackle sound with low bit depth
                    crackleNoise.gain(lowBitDepth(Math.random2f(0.0, 0.5), 8));
                    
                    50::ms => now;
                }}
            """

        elif self.current_sound == "Summer Night":
            chuck_script = f"""
                // Summer night with cicada sounds

                Gain g => dac;
                g.gain({self.volume}); // Set overall gain

                // Cicada sound function
                fun void cicada() {{
                    SinOsc s => LPF f => JCRev r => g; // Low-pass filter and reverb
                    s.freq(3000); // High-pitched frequency typical of cicadas
                    0.01 => s.gain;
                    80 => f.freq; // Set filter cutoff frequency
                    0.1 => r.mix; // Set reverb mix
                    while (true) {{
                        // Random chirping duration
                        Math.random2f(0.01, 0.03) :: second => dur chirp_dur;
                        // Randomize frequency slightly
                        s.freq(3000 + Math.random2f(-100, 100));
                        Math.random2f(0.05, 0.2) => s.gain;
                        chirp_dur => now;
                    }}
                }}

                // Instantiate multiple cicadas
                for (0 => int i; i < 10; i++) {{
                    spork ~ cicada();
                    0.2::second => now; // Stagger start times
                }}

                // Background noise (e.g., wind or distant traffic)
                Noise n => LPF f => JCRev r => g; // Low-pass filter and reverb
                1.1 => n.gain;
                500 => f.freq; // Set filter cutoff frequency
                0.2 => r.mix; // Set reverb mix
                while (true) {{
                    Math.random2f(0.005, 0.01) => n.gain;
                    1::second => now;
                }}
            """

        elif self.current_sound == "Jungle Noise":
            chuck_script = f"""
                // Jungle noise with birds and insects

                Gain g => dac;
                g.gain({self.volume}); // Set overall gain

                // Function to create a bird chirp sound
                fun void bird_chirp() {{
                    SinOsc bird => ADSR env => g;
                    1200 + Math.random2f(300, 800) => bird.freq; // Randomize frequency
                    env.set(0.01, 0.2, 0.2, 0.01); // Envelope settings
                    0.2 => bird.gain;
                    env.keyOn();
                    0.3::second => now; // Chirp duration
                    env.keyOff();
                    0.1::second => now; // Short pause
                }}

                // Function to create an insect sound
                fun void insect_sound() {{
                    Noise insect => BPF filter => ADSR env => g;
                    5000 + Math.random2f(1000, 2000) => filter.freq; // Randomize frequency
                    1.01 => filter.Q;
                    env.set(0.01, 0.1, 0.2, 0.01); // Envelope settings
                    0.1 => insect.gain; // Apply a 20 dB pad
                    env.keyOn();
                    0.2::second => now; // Insect sound duration
                    env.keyOff();
                    0.3::second => now; // Short pause
                }}

                // Function to create water stream sound
                fun void water_stream() {{
                    Noise water => LPF filter => g;
                    2000 => filter.freq; // Low-pass filter for water sound
                    0.2 => filter.Q;
                    0.1 => water.gain;
                    while (true) {{
                        0.1::second => now;
                    }}
                }}

                // Spork water stream sound continuously
                spork ~ water_stream();

                // Main loop for bird chirps and insect sounds
                while (true) {{
                    // Randomly decide to play a bird chirp or insect sound
                    if (Math.random2f(0, 1) < 0.5) {{
                        spork ~ bird_chirp();
                    }} else {{
                        spork ~ insect_sound();
                    }}
                    // Random interval between sounds
                    0.5::second + Math.random2f(1, 3)::second => now;
                }}
            """

        elif self.current_sound == "Inside Volcano":
            chuck_script = f"""
                // Inside Volcano

                // Gain control
                Gain g => dac;

                // Delay and reverb effects
                Delay delay => JCRev reverb => g;
                delay.delay(1::ms);    // Set delay time
                reverb.mix(0.5);       // Set reverb mix

                // Noise generator for continuous low rumble
                Noise rumbleNoise => HPF hpfRumble => LPF lpfRumble => delay;
                hpfRumble.freq(20.0);  // High-pass to remove ultra-low frequencies
                lpfRumble.freq(100.0); // Low-pass to focus on the low rumble

                // Noise generator for popping sounds
                Noise popNoise => BPF bpfPop => delay;
                bpfPop.freq(500.0);
                bpfPop.Q(10.0);

                // Sine oscillators for bubbling effect
                SinOsc bubbleOsc1 => Gain bubbleGain1 => delay;
                bubbleOsc1.freq(0.5);

                SinOsc bubbleOsc2 => Gain bubbleGain2 => delay;
                bubbleOsc2.freq(0.7);

                // Set overall gain
                g.gain({self.volume});

                // Infinite loop to keep the sound playing
                while (true) {{
                    // Generate continuous rumble sound
                    0.2 + 0.1 * Math.sin(now / second) => rumbleNoise.gain;
                    
                    // Generate tar bubble popping sound
                    if (Math.random2(0, 100) < 20) {{ // Randomly trigger pops
                        0.5 + 0.5 * Math.random2f(-1.0, 1.0) => popNoise.gain;
                        10::ms => now;
                        0.0 => popNoise.gain;
                    }}
                    
                    // Modulate bubbling oscillators
                    (0.5 + 0.5 * bubbleOsc1.last() + 0.5 * bubbleOsc2.last()) * Math.random2f(0.1, 0.3) => bubbleGain1.gain;
                    (0.5 + 0.5 * bubbleOsc2.last() + 0.5 * bubbleOsc1.last()) * Math.random2f(0.1, 0.3) => bubbleGain2.gain;
                    
                    50::ms => now;
                }}
            """

        with open("sound_script.ck", "w") as f:
            f.write(chuck_script)
        self.chuck_process = subprocess.Popen(["chuck", "sound_script.ck"])
        self.update_button_color()

    def update_button_color(self):
        if self.chuck_process:
            amplitude = 0.5 + 0.4 * sin(0.2) + 0.2 * sin(0.1)
            color_intensity = int(amplitude * 255)
            color_hex = f'#{color_intensity:02x}0000'
            self.central_button.configure(bg=color_hex)
            self.root.after(100, self.update_button_color)  # Update color every 100 ms

if __name__ == "__main__":
    root = tk.Tk()
    root.attributes('-fullscreen', True)  # Make the window fullscreen
    app = SoundMachineApp(root)
    root.mainloop()
