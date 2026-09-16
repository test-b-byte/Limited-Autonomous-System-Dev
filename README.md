# LAS Application 2A: Distributed Sensor-For Defense Operations
 
A wearable camera+display system that lets a soldier (or rescue worker) get a live, annotated overhead view from a drone, in one eye, while keeping the other eye and their normal awareness free. This is the first working piece of a larger idea, **LAS**, described below.
 
## About LAS
 
**Limited Autonomous Systems (LAS)** is a systems-design concept for building cheap, attributable, distributed autonomous capability, not a single product. Both allied forces (notably Ukraine) and adversarial actors have demonstrated the strategic advantage of cheap, attributable systems over exquisite, high-cost platforms. U.S. and NATO doctrine has largely bet on AI-enabled autonomy and higher-quality systems to offset peer and near-peer adversaries' larger personnel reserves. In practice, this has produced a kind of pseudo-innovation spiral: companies promise advanced autonomous capability, but the actual enabling advantage of small autonomous platforms, drones included, is low mass and low cost, not intelligence.
 
Battery mass dominates the weight budget of most small autonomous systems. Onboard AI, autonomous sensing, computing, and decision-making are energetically expensive, and stacking additional embedded payloads compounds the problem. The typical industry response is to scale the vehicle up to carry the added weight and power, which makes the platform more expensive, more targetable, less attributable, and ultimately erodes the original advantage the system was built to exploit: **cost**. Systems need to stay cheap, disposable, and attributable while still expanding what they're capable of doing.
 
This is the premise behind **LAS**: rather than centralizing sensing, decision-making, and actuation onboard a single increasingly expensive platform, distribute those responsibilities across the nodes of a broader operational system. Vehicle, sensor payload, and human operator each handle only the piece they're best suited for. Decision-compute is concentrated at a small number of key junctions rather than spread across a fully autonomous stack. LAS is the overarching company concept; individual applications built under it (like this one) each address one piece of that distributed-node problem.
 
A concrete gap motivating LAS: in conversations with operators in Ukraine, a recurring complaint has been a shortage of trained pilots for interceptor drones, combined with very short flight windows and a narrow window of effect. Adding onboard AI to compensate hasn't been a clean win. It either costs flight time (battery draw) or costs unit economics (reproducibility, cost per unit). The open gap is autonomous systems capable of handling the full chain of tasks a human currently must, including mapping, orienteering, positioning, sensing/recognition, and flight, without abandoning the low-cost, attributable profile that makes these systems valuable in the first place.
 
## LAS Technology Pillars
 
LAS targets three critical areas where effective sensing, command structure, and human-machine collaboration bottleneck the broader goal of cheap, attributable, distributed systems.
 
### 1. Sensing & ML
 
Hyper-sophisticated sensing paired with autonomous or robotic systems depends on two key improvements:
 
1. **Simulation training engines**
2. **Targeting data libraries**
Once the correct pain point or decision junction is identified, building the targeting data libraries becomes an immediate, reachable development goal for that LAS application. Simulation engines serve a dual purpose: training the system, and establishing the sandboxed state space for a tightly constrained use case. In essence, the goal is to maximize operational effectiveness while minimizing the cost of implementation and reproduction, referred to internally as the **Consolidation Phase**.
 
In Application 2A's case, these libraries are what would ultimately drive the drone's observation capability and the extended-reality contributions overlaid on the operator's screen: better identifying and locating gunshots, movement, and other points of interest from a high, standoff vantage point. A relay architecture is one way to keep that data flowing reliably. An elevated observation drone pairs with a lower, ground-proximal relay drone near the C2 node over a physical fiber-optic link, so that jamming aimed at conventional RF links doesn't cut soldiers off from real-time updates. The observation drone still needs a wireless hop to reach the relay, but keeping that hop short and low-altitude reduces the exposed attack surface relative to a single long-range RF link running the whole distance back to the operator.
 
### 2. C2 and Operations-Centered Focus
 
As technology changes the character of conflict, established command structures are shifting. Command and control (C2) is no longer a squad leader and a radio operator sitting in a fixed rear position. It is becoming the information-dissemination axis of a small swarm or Limited Autonomous fighting force, and that axis is decentralizing out toward the tactical edge rather than staying concentrated at a single command post.
 
At the same time, soldiers themselves are pulling further away from that edge, even as they're more heavily augmented by autonomous systems and new sensing technology than at any point before. The same forces pushing the decision node outward, toward the point of contact, are also allowing the human operator to stand back from direct physical exposure. Application 2A's wearable display is a small-scale illustration of this shift: the "C2 node" becomes whoever is wearing the monocle, receiving a fused, elevated view of the battlespace directly, rather than a fixed command post piecing the picture together secondhand from radio reports.
 
### 3. Human-Machine Teaming
 
Ukrainian interceptor drone operations illustrate this directly: can a single pilot reliably fly seven interceptors into the same airspace, iterating rapidly between them so that only one aircraft is actively making a decision or activating its payload at any given moment, while the rest simply hold designated airspace or return home unless tagged in? Human-Machine Teaming is the operational implementation of that question. The human's role shifts from piloting one platform to supervising and selectively engaging across several.
 
## Application 2A
 
**Application 2A** is LAS's first fielded expression of the concept above: a low-cost wearable feedback system that offloads sensing, recognition, and situational-awareness display to a human operator, rather than requiring the vehicle itself to carry the full autonomy stack.
 
The end product vision: an elevated observation drone feeds a real-time, annotated bird's-eye feed to a wearable monocle display worn by a ground operator, with gunshots, movement, and other points of interest highlighted automatically via computer vision and audio event detection. It is a direct, simple expression of all three Technology Pillars. Its sensing and audio-event pipeline is a hands-on testbed for the Sensing & ML pillar; its wearer effectively becomes a mobile, edge-based C2 node rather than relying on a fixed command post, in line with the C2 and Operations-Centered Focus pillar; and its human-in-the-loop design (one operator interpreting a fused feed rather than a fully autonomous system making the call) is a simple expression of Human-Machine Teaming.
 
The vision is a system buildable for a few thousand dollars that gives a fire team real-time battlefield awareness previously unavailable at this cost point. The two hardest pieces left to build are: a comms link that keeps working under jamming, and enough real sound/video data to train the system to actually recognize things like gunshots reliably (see Roadmap, Phases 2-3). Neither exists yet, even at prototype scale. Both are solvable engineering work, not something blocked by unsolved science — that's what makes this a build-it problem, not a research bet.
 
Application 2A is also dual-use by design: the identical sensor-fusion/situational-awareness pipeline applies directly to search-and-rescue and disaster-response contexts, not exclusively defense applications.
 
## Current Prototype
 
The current build is an early proof-of-concept, originally developed as an embedded systems project, and is the starting point for the product vision above, not the finished product. It intentionally simplifies several things that a fielded Application 2A would need to solve:
 
- No drone piloting. A fixed or hand-held camera rig stands in for an airborne platform, since the sensing/feedback pipeline is what's under test, not flight.
- No field-durable housing. 3D-printed brackets, a servo swing-arm, and benchtop wiring stand in for a ruggedized enclosure.
- No touch input, no onboard networking beyond what's needed for the demo.
Battery and power draw **are** treated as a real design constraint from the start, since a fielded Application 2A depends on it, even though solving it fully in hardware is out of scope for the current prototype (see [Power Budget](#power-budget)).
 
## System Architecture
 
```
[Camera] ──────────────┐
                        ├──→ [CV processing thread] ──┐
[Microphone] ──────────┘                              ├──→ [Overlay compositing] ──→ [SPI display on servo arm]
                                                        │
                        [Audio event-detection thread] ─┘
```
 
- Capture, processing, and display run as separate threads/processes (producer-consumer pattern) so a slow display write never stalls detection.
- Motion/event detection uses OpenCV background subtraction as the baseline approach; a lightweight model (e.g. MobileNet-SSD/TFLite) is a stretch goal if time and Pi 4B performance allow.
- Audio detection flags sudden, sharp sounds (a technique called onset/spectral-flux detection) independently of the video pipeline, fused into the same overlay. Right now it can tell "something sharp just happened" but not "that was a gunshot" — turning that into real gunshot recognition needs a trained model and real recorded sound data, which is Phase 3 work (see Roadmap), not something the current method does on its own.
## Hardware
 
| Component | Selection | Notes |
|---|---|---|
| Compute | Raspberry Pi 4B | |
| Display | Waveshare 3.5" Resistive Touch IPS LCD, XPT2046, SPI, 480x320 | Chosen over HDMI alternatives for lower power draw (~150mA vs ~500mA) and current availability; touch unused |
| Camera | Pi Camera Module (CSI) or USB webcam | TBD based on availability |
| Microphone | USB or I2S mic | TBD |
| Actuation | SG90 micro servo on GPIO18 (hardware PWM) | Swings display between two fixed positions |
| Mount | 3D-printed bracket | Not field-durable by design at this stage |
 
### Why SPI over HDMI
 
HDMI was the initial pick for its higher framerate ceiling (20-30fps+ vs. SPI's ~15-25fps) and driver-free setup. The decision reversed once suitable HDMI panels were consistently out of stock, and once the fieldability goal made SPI's much lower current draw (~150mA vs ~500mA) directly relevant rather than a nice-to-have. This tradeoff, framerate and setup simplicity against power budget, is itself a small-scale illustration of the cost/capability tension described in the LAS motivation above.
 
## Power Budget
 
*(fill in as measured; track idle and active current draw per component with an inline USB power meter)*
 
| Component | Idle draw | Active draw | Source |
|---|---|---|---|
| Raspberry Pi 4B | | | measured |
| SPI display | ~150mA @ 5V (mfr. spec) | | Waveshare docs |
| Camera | | | measured |
| Servo | | | measured, isolate on separate rail |
| **Total** | | | |
 
## Setup
 
1. Enable camera interface: `sudo raspi-config` → Interface Options → Camera → reboot
2. Install SPI display driver (Waveshare `dtoverlay`/LCD-show script); confirm `/dev/fb1` exists
3. Install dependencies: `sudo apt install -y python3-picamera2 python3-opencv python3-numpy`
4. Confirm the framebuffer test image displays before adding camera code; this isolates display issues from camera issues
5. Servo on GPIO18 (physical pin 12), powered from a separate 5V rail with shared ground to the Pi. Do not power the servo from the Pi's 5V GPIO pin directly (brownout risk).
## Roadmap
 
**Phase 1: Proof of Concept (current)**
- [ ] Camera capture to SPI display, raw passthrough (no CV)
- [ ] Motion detection and overlay compositing
- [ ] Audio event detection integrated
- [ ] Servo-driven swing-arm mount
- [ ] Power budget table completed with real measurements
**Phase 2: Field Hardening**
- Field-durable enclosure and mounting
- Battery power budget and runtime targeting, informed by Phase 1's measured draw
- Directional audio localization (multi-mic array) rather than generic event flagging
**Phase 3: Full Sensing Integration**
- Elevated/birds-eye perspective mapping (requires a second camera angle or projective transform)
- Fiber-optic relay drone pairing to reduce dependence on long-range RF links vulnerable to jamming (a real-world approach already seeing use in Ukraine)
- Targeting data library and simulation-engine development to expand recognition capability
**Beyond Application 2A**
- Additional LAS applications, addressing other distributed-node roles within the broader system, are out of scope for this document
 
