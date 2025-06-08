# 

<p align="center">
  <img src="img/oqd-logo-black.png#only-light" alt="Logo" style="max-height: 200px;">
  <img src="img/oqd-logo-white.png#only-dark" alt="Logo" style="max-height: 200px;">
</p>

<div align="center">
    <h2 align="center">
    Open Quantum Design: Documentation
    </h2>
</div>

Welcome to the Open Quantum Design (OQD) documentation! 
Open Quantum Design is a non-profit foundation supporting the development of full-stack, open-source quantum computers.
OQD's current designs are based on laser-cooled trapped ion quantum computing hardware, including real-time control, backend and frontend software.
This documentation covers the software components of the OQD stack, including the core programming interfaces,
classical emulation backends, compiler infrastructure, and cloud server containers.

## The stack
OQD's quantum computer stack can be interfaced at different levels, including the digital layer, analog layer, and atomic layer.
```mermaid
block-beta
   columns 3

   block:Interface
       columns 1
       InterfaceTitle("<i><b>Interfaces</b><i/>")
       InterfaceDigital["<b>Digital Interface</b>\nQuantum circuits with discrete gates"]
       space
       InterfaceAnalog["<b>Analog Interface</b>\n Continuous-time evolution with Hamiltonians"]
       space
       InterfaceAtomic["<b>Atomic Interface</b>\nLight-matter interactions between lasers and ions"]
       space
    end

    block:IR
       columns 1
       IRTitle("<i><b>IRs</b><i/>")
       IRDigital["Quantum circuit IR\nopenQASM, LLVM+QIR"]
       space
       IRAnalog["openQSIM"]
       space
       IRAtomic["openAPL"]
       space
    end

    block:Emulator
       columns 1
       EmulatorsTitle("<i><b>Classical Emulators</b><i/>")

       EmulatorDigital["Pennylane, Qiskit"]
       space
       EmulatorAnalog["QuTiP, QuantumOptics.jl"]
       space
       EmulatorAtomic["TrICal, QuantumIon.jl"]
       space
    end

    space
    block:RealTime
       columns 1
       RealTimeTitle("<i><b>Real-Time</b><i/>")
       space
       RTSoftware["ARTIQ, DAX, OQDAX"]
       space
       RTGateware["Sinara Real-Time Control"]
       space
       RTHardware["Lasers, Modulators, Photodetection, Ion Trap"]
       space
       RTApparatus["Trapped-Ion QPU (<sup>171</sup>Yb<sup>+</sup>, <sup>133</sup>Ba<sup>+</sup>)"]
       space
    end
    space

   InterfaceDigital --> IRDigital
   InterfaceAnalog --> IRAnalog
   InterfaceAtomic --> IRAtomic

   IRDigital --> IRAnalog
   IRAnalog --> IRAtomic

   IRDigital --> EmulatorDigital
   IRAnalog --> EmulatorAnalog
   IRAtomic --> EmulatorAtomic

   IRAtomic --> RealTimeTitle

   RTSoftware --> RTGateware
   RTGateware --> RTHardware
   RTHardware --> RTApparatus

   classDef title fill:#23627D,stroke:#141414,color:#FFFFFF;
   classDef digital fill:#c3e1ee,stroke:#141414,color:#141414;
   classDef analog fill:#afd7e9,stroke:#141414,color:#141414;
   classDef atomic fill:#9ccee3,stroke:#141414,color:#141414;
   classDef realtime fill:#88c4dd,stroke:#141414,color:#141414;

   classDef highlight fill:#F19D19,stroke:#141414,color:#141414,stroke-dasharray: 5 5;
   classDef normal fill:#fcebcf,stroke:#141414,color:#141414;

   class InterfaceTitle,IRTitle,EmulatorsTitle,RealTimeTitle title
   class InterfaceDigital,IRDigital,EmulatorDigital digital
   class InterfaceAnalog,IRAnalog,EmulatorAnalog analog
   class InterfaceAtomic,IRAtomic,EmulatorAtomic atomic
   class RTSoftware,RTGateware,RTHardware,RTApparatus realtime


   class Emulator normal
   class IR normal
   class RealTime normal
   class Interface normal
```


## Getting Started <a name="Getting Started"></a>
Here's a short example of how to use the analog interface to specify, serialize, and simulate an analog quantum program.
We use a simple, single-qubit Rabi-flopping experiment as an example:
```python
from oqd_core.interface.analog.operator import PauliZ, PauliX
from oqd_core.interface.analog.operation import AnalogCircuit, AnalogGate
from oqd_core.backend.metric import Expectation
from oqd_core.backend.task import Task, TaskArgsAnalog
from oqd_analog_emulator.qutip_backend import QutipBackend

X = PauliX()
Z = PauliZ()

Hx = AnalogGate(hamiltonian=X)

circuit = AnalogCircuit()
circuit.evolve(duration=10, gate=Hx)
circuit.measure()

args = TaskArgsAnalog(
  n_shots=100,
  fock_cutoff=4,
  metrics={"Z": Expectation(operator=Z)},
  dt=1e-3,
)

task = Task(program=circuit, args=args)

backend = QutipBackend()
results = backend.run(task=task)
```
