# Brain-to-Brain Interface Simulation Framework

A comprehensive simulation framework for closed-loop, real-time affective brain-to-brain interfaces, supporting multi-channel EEG signal generation, ML-based decoding, and neurostimulation response modeling.

## Overview

This framework provides a modular, configurable platform for simulating brain-to-brain interfaces (BBI) with the following components:

1. **EmotionSignal**: Generates multi-channel emotion signals with configurable waveforms, noise, and artifacts
2. **Decoder**: Decodes EEG signals using simple averaging or ML-based approaches (SVM, Random Forest, MLP)
3. **Stimulator**: Simulates neurostimulation responses with configurable parameters
4. **BBISimulation**: Integrates all components into a closed-loop simulation
5. **ParameterSweep**: Runs sensitivity analyses over parameter grids

The framework is designed for research and publication purposes, with comprehensive metrics, visualization tools, and manuscript-ready outputs.

## Installation

### Requirements

- Python 3.9 or 3.10
- Required packages: numpy, scipy, matplotlib, scikit-learn, pandas, pyyaml, seaborn

### Installation Steps

1. Clone the repository:
```bash
git clone https://github.com/MohanadAlmahjoub/bbi_simulation
cd bbi_simulation
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. Install the package in development mode:
```bash
pip install -e .
```

## Configuration

The framework is configured using YAML files. The default configuration is located at `config/default_config.yaml`. You can create your own configuration file by copying and modifying this file.

Key configuration sections:

- **simulation**: General simulation parameters (duration, time step, latency jitter)
- **emotion_signal**: Signal generation parameters (waveform type, frequency, dimensions, channels)
- **decoder**: Decoding parameters (type, window size, noise, ML model settings)
- **stimulator**: Stimulation parameters (time constant, threshold)
- **analysis**: Parameter sweep settings (parameters to sweep, repetitions)

Example configuration:

```yaml
simulation:
  duration: 10.0
  dt: 0.01
  latency_jitter: 0.01

emotion_signal:
  kind: "sine"
  freq: 0.2
  dims: 2
  channels: 8
  channel_names: ["Fp1", "Fp2", "F3", "F4", "C3", "C4", "P3", "P4"]
  noise:
    gaussian:
      enabled: true
      std: 0.1
    powerline:
      enabled: true
      freq: 50
      amplitude: 0.05

decoder:
  type: "ml"
  window_size: 50
  ml:
    model_type: "auto"
    cv_folds: 5

stimulator:
  tau: 0.1
  threshold: 0.0
```

## Usage

### Basic Simulation

To run a basic simulation with default parameters:

```python
from bbi_simulation.simulation.bbi_simulation import BBISimulation

# Create and run simulation
sim = BBISimulation()
results = sim.run()

# Access results
t = results['t']
emotion_signals = results['emotion_signals']
channel_signals = results['channel_signals']
decoded = results['decoded']
response = results['response']
metrics = results['metrics']

# Print metrics
print(f"Mean decode correlation: {metrics['mean_decode_correlation']:.4f}")
print(f"Mean response correlation: {metrics['mean_correlation']:.4f}")
```

### Parameter Sweep

To run a parameter sweep:

```python
from bbi_simulation.analysis.parameter_sweep import ParameterSweep

# Create parameter sweep
sweep = ParameterSweep(output_dir='results/parameter_sweep')

# Run parameter sweep
results = sweep.run()

# Generate plots
plot_files = sweep.generate_plots()
```

### Command-line Interface

The framework also provides a command-line interface:

```bash
# Run a single simulation
python -m bbi_simulation.main --config my_config.yaml --output results

# Run a parameter sweep
python -m bbi_simulation.main --sweep --config my_config.yaml --output results/sweep
```

## Module Documentation

### EmotionSignal

The `EmotionSignal` class generates synthetic emotion signals with different waveforms across multiple channels.

```python
from bbi_simulation.core.emotion_signal import EmotionSignal
from bbi_simulation.config.config_loader import ConfigLoader

# Load configuration
config_loader = ConfigLoader()
config = config_loader.get_section('emotion_signal')

# Create signal generator
signal_gen = EmotionSignal(config)

# Generate signals
emotion_signals, channel_signals = signal_gen.generate(t)
```

Key features:
- Multiple waveform types (sine, square, synthetic data)
- Multi-channel support with configurable amplitudes
- Gaussian noise and power-line artifacts
- External data loading (CSV, EDF)

### Decoder

The `Decoder` class decodes EEG signals into emotion values using simple averaging or ML-based approaches.

```python
from bbi_simulation.core.decoder import Decoder

# Create decoder
decoder = Decoder(config)

# Decode signals
decoded = decoder.decode(eeg_chunk)

# Train ML models
results = decoder.train(X_train, y_train)
```

Key features:
- Simple averaging with configurable noise
- ML-based decoding with multiple model types (SVM, Random Forest, MLP)
- Model selection based on cross-validated F1-score
- Model persistence (save/load)

### Stimulator

The `Stimulator` class simulates neurostimulation responses using an exponential decay kernel.

```python
from bbi_simulation.core.stimulator import Stimulator

# Create stimulator
stimulator = Stimulator(config)

# Stimulate
response = stimulator.stimulate(decoded)

# Compute metrics
metrics = stimulator.compute_response_metrics(original, response)
```

Key features:
- Configurable time constant (tau)
- Activation threshold
- Response metrics (correlation, RMSE, delay)

### BBISimulation

The `BBISimulation` class integrates all components into a closed-loop simulation.

```python
from bbi_simulation.simulation.bbi_simulation import BBISimulation

# Create simulation
sim = BBISimulation(config_path='my_config.yaml', log_dir='results')

# Run simulation
results = sim.run(real_time=False)
```

Key features:
- End-to-end simulation pipeline
- Real-time or accelerated simulation
- Comprehensive logging and metrics
- Result visualization

### ParameterSweep

The `ParameterSweep` class runs sensitivity analyses over parameter grids.

```python
from bbi_simulation.analysis.parameter_sweep import ParameterSweep

# Create parameter sweep
sweep = ParameterSweep(config_path='my_config.yaml', output_dir='results/sweep')

# Run parameter sweep
results = sweep.run()

# Generate plots
plot_files = sweep.generate_plots()
```

Key features:
- Parameter grid generation
- Multiple simulation runs
- CSV output
- Summary plots for publication

## Examples

See the `examples` directory for example scripts and notebooks:

- `examples/basic_simulation.py`: Basic simulation example
- `examples/parameter_sweep.py`: Parameter sweep example
- `examples/demo.ipynb`: Interactive demo notebook

## Testing

Run the tests using pytest:

```bash
pytest
```

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Citation

If you use this framework in your research, please cite:

```
@article{bbi_simulation,
  title={A Digital Simulation Platform for Closed-Loop Brain-to-Brain Interfaces},
  author={Your Name},
  journal={Journal Name},
  year={2025}
}
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
