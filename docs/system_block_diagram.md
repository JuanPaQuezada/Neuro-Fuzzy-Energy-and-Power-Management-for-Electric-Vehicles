The neuro-fuzzy system operates as a supervisory decision layer, generating smooth power limitation commands based on battery state and driving behavior, while all safety-critical protections remain enforced by deterministic BMS logic.

flowchart LR

%% Input Layer
A[Battery Sensors<br/>SoC, Temperature] --> B[Signal Conditioning<br/>& Normalization]
C[Vehicle Dynamics<br/>Acceleration, Braking, Lateral G] --> B

%% Driving Style
B --> D[Driving Style Estimation]
D -->|Normalized Index 0-1| E

%% Neuro-Fuzzy Core
B --> E[Neuro-Fuzzy Controller<br/>(ANFIS - Mamdani)]
E --> F[SOP Limit<br/>(Power Derating Factor)]

%% Output
F --> G[Vehicle Control System<br/>(Inverter / Powertrain)]

%% Safety Layer
H[Hard Safety Constraints<br/>(BMS Protection Logic)]
H --> G
H -. Overrides .-> E
