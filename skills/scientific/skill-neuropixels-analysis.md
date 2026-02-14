---
id: skill-neuropixels-analysis
type: skill
name: neuropixels-analysis
description: Neuropixels neural recording analysis. Load SpikeGLX/OpenEphys data,
  preprocess, motion correction, Kilosort4 spike sorting, quality metrics, Allen/IBL
  curation, AI-assisted visual analysis, for Neuropixels 1.0/2.0 extracellular electrophysiology.
  Use when working with neural recordings, spike sorting, extracellular electrophysiology,
  or when the user mentions Neuropixels, SpikeGLX, Open Ephys, Kilosort, quality metrics,
  or unit curation.
category: scientific
complexity: medium
keywords:
- api
- github
- python
- testing
capabilities: []
token_estimate: 1534
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,534 -->
<!-- Includes Reference Materials -->

> **How to Use This Template**
>
> This template can be used with various AI coding assistants:
> 
> **GitHub Copilot:**
> - Add to `.github/copilot-instructions.md` in your repository
> - Reference in chat: `@workspace` to include in context
> - Add specific sections to your workspace instructions
> 
> **Augment Code:**
> - Load context: `aug context add <path-to-this-file>`
> - Reference in queries naturally
> - Use with specific commands
> 
> **Claude (Desktop/Web):**
> - Add to Project Knowledge in Claude Projects
> - Reference in custom instructions
> - Copy relevant sections as needed
> 
> **ChatGPT:**
> - Add to Custom GPT configuration
> - Include in conversation instructions
> - Use as system prompt
> 
> **Generic Usage:**
> Copy the content below and paste it into your AI assistant's context window
> or system instructions.

---




# neuropixels-analysis

> Neuropixels neural recording analysis. Load SpikeGLX/OpenEphys data, preprocess, motion correction, Kilosort4 spike sorting, quality metrics, Allen/IBL curation, AI-assisted visual analysis, for Neuropixels 1.0/2.0 extracellular electrophysiology. Use when working with neural recordings, spike sorting, extracellular electrophysiology, or when the user mentions Neuropixels, SpikeGLX, Open Ephys, Kilosort, quality metrics, or unit curation.

# Neuropixels Data Analysis

## Overview

Comprehensive toolkit for analyzing Neuropixels high-density neural recordings using current best practices from SpikeInterface, Allen Institute, and International Brain Laboratory (IBL). Supports the full workflow from raw data to publication-ready curated units.

## When to Use This Skill

This skill should be used when:
- Working with Neuropixels recordings (.ap.bin, .lf.bin, .meta files)
- Loading data from SpikeGLX, Open Ephys, or NWB formats
- Preprocessing neural recordings (filtering, CAR, bad channel detection)
- Detecting and correcting motion/drift in recordings
- Running spike sorting (Kilosort4, SpykingCircus2, Mountainsort5)
- Computing quality metrics (SNR, ISI violations, presence ratio)
- Curating units using Allen/IBL criteria
- Creating visualizations of neural data
- Exporting results to Phy or NWB

## Supported Hardware & Formats

| Probe | Electrodes | Channels | Notes |
|-------|-----------|----------|-------|
| Neuropixels 1.0 | 960 | 384 | Requires phase_shift correction |
| Neuropixels 2.0 (single) | 1280 | 384 | Denser geometry |
| Neuropixels 2.0 (4-shank) | 5120 | 384 | Multi-region recording |

| Format | Extension | Reader |
|--------|-----------|--------|
| SpikeGLX | `.ap.bin`, `.lf.bin`, `.meta` | `si.read_spikeglx()` |
| Open Ephys | `.continuous`, `.oebin` | `si.read_openephys()` |
| NWB | `.nwb` | `si.read_nwb()` |

## Quick Start

### Basic Import and Setup

```python
import spikeinterface.full as si
import neuropixels_analysis as npa

# Configure parallel processing
job_kwargs = dict(n_jobs=-1, chunk_duration='1s', progress_bar=True)
```

### Loading Data

```python
# SpikeGLX (most common)
recording = si.read_spikeglx('/path/to/data', stream_id='imec0.ap')

# Open Ephys (common for many labs)
recording = si.read_openephys('/path/to/Record_Node_101/')

# Check available streams
streams, ids = si.get_neo_streams('spikeglx', '/path/to/data')
print(streams)  # ['imec0.ap', 'imec0.lf', 'nidq']

# For testing with subset of data
recording = recording.frame_slice(0, int(60 * recording.get_sampling_frequency()))
```

### Complete Pipeline (One Command)

```python
# Run full analysis pipeline
results = npa.run_pipeline(
    recording,
    output_dir='output/',
    sorter='kilosort4',
    curation_method='allen',
)

# Access results
sorting = results['sorting']
metrics = results['metrics']
labels = results['labels']
```

## Standard Analysis Workflow

### 1. Preprocessing

```python
# Recommended preprocessing chain
rec = si.highpass_filter(recording, freq_min=400)
rec = si.phase_shift(rec)  # Required for Neuropixels 1.0
bad_ids, _ = si.detect_bad_channels(rec)
rec = rec.remove_channels(bad_ids)
rec = si.common_reference(rec, operator='median')

# Or use our wrapper
rec = npa.preprocess(recording)
```

### 2. Check and Correct Drift

```python
# Check for drift (always do this!)
motion_info = npa.estimate_motion(rec, preset='kilosort_like')
npa.plot_drift(rec, motion_info, output='drift_map.png')

# Apply correction if needed
if motion_info['motion'].max() > 10:  # microns
    rec = npa.correct_motion(rec, preset='nonrigid_accurate')
```

### 3. Spike Sorting

```python
# Kilosort4 (recommended, requires GPU)
sorting = si.run_sorter('kilosort4', rec, folder='ks4_output')

# CPU alternatives
sorting = si.run_sorter('tridesclous2', rec, folder='tdc2_output')
sorting = si.run_sorter('spykingcircus2', rec, folder='sc2_output')
sorting = si.run_sorter('mountainsort5', rec, folder='ms5_output')

# Check available sorters
print(si.installed_sorters())
```

### 4. Postprocessing

```python
# Create analyzer and compute all extensions
analyzer = si.create_sorting_analyzer(sorting, rec, sparse=True)

analyzer.compute('random_spikes', max_spikes_per_unit=500)
analyzer.compute('waveforms', ms_before=1.0, ms_after=2.0)
analyzer.compute('templates', operators=['average', 'std'])
analyzer.compute('spike_amplitudes')
analyzer.compute('correlograms', window_ms=50.0, bin_ms=1.0)
analyzer.compute('unit_locations', method='monopolar_triangulation')
analyzer.compute('quality_metrics')

metrics = analyzer.get_extension('quality_metrics').get_data()
```

### 5. Curation

```python
# Allen Institute criteria (conservative)
good_units = metrics.query("""
    presence_ratio > 0.9 and
    isi_violations_ratio < 0.5 and
    amplitude_cutoff < 0.1
""").index.tolist()

# Or use automated curation
labels = npa.curate(metrics, method='allen')  # 'allen', 'ibl', 'strict'
```

### 6. AI-Assisted Curation (For Uncertain Units)

When using this skill with Claude Code, Claude can directly analyze waveform plots and provide expert curation decisions. For programmatic API access:

```python
from anthropic import Anthropic

# Setup API client
client = Anthropic()

# Analyze uncertain units visually
uncertain = metrics.query('snr > 3 and snr < 8').index.tolist()

for unit_id in uncertain:
    result = npa.analyze_unit_visually(analyzer, unit_id, api_client=client)
    print(f"Unit {unit_id}: {result['classification']}")
    print(f"  Reasoning: {result['reasoning'][:100]}...")
```

**Claude Code Integration**: When running within Claude Code, ask Claude to examine waveform/correlogram plots directly - no API setup required.

### 7. Generate Analysis Report

```python
# Generate comprehensive HTML report with visualizations
report_dir = npa.generate_analysis_report(results, 'output/')
# Opens report.html with summary stats, figures, and unit table

# Print formatted summary to console
npa.print_analysis_summary(results)
```

### 8. Export Results

```python
# Export to Phy for manual review
si.export_to_phy(analyzer, output_folder='phy_export/',
                 compute_pc_features=True, compute_amplitudes=True)

# Export to NWB
from spikeinterface.exporters import export_to_nwb
export_to_nwb(rec, sorting, 'output.nwb')

# Save quality metrics
metrics.to_csv('quality_metrics.csv')
```

## Common Pitfalls and Best Practices

1. **Always check drift** before spike sorting - drift > 10μm significantly impacts quality
2. **Use phase_shift** for Neuropixels 1.0 probes (not needed for 2.0)
3. **Save preprocessed data** to avoid recomputing - use `rec.save(folder='preprocessed/')`
4. **Use GPU** for Kilosort4 - it's 10-50x faster than CPU alternatives
5. **Review uncertain units manually** - automated curation is a starting point
6. **Combine metrics with AI** - use metrics for clear cases, AI for borderline units
7. **Document your thresholds** - different analyses may need different criteria
8. **Export to Phy** for critical experiments - human oversight is valuable

## Key Parameters to Adjust

### Preprocessing
- `freq_min`: Highpass cutoff (300-400 Hz typical)
- `detect_threshold`: Bad channel detection sensitivity

### Motion Correction
- `preset`: 'kilosort_like' (fast) or 'nonrigid_accurate' (better for severe drift)

### Spike Sorting (Kilosort4)
- `batch_size`: Samples per batch (30000 default)
- `nblocks`: Number of drift blocks (increase for long recordings)
- `Th_learned`: Detection threshold (lower = more spikes)

### Quality Metrics
- `snr_threshold`: Signal-to-noise cutoff (3-5 typical)
- `isi_violations_ratio`: Refractory violations (0.01-0.5)
- `presence_ratio`: Recording coverage (0.5-0.95)

## Bundled Resources

### scripts/preprocess_recording.py
Automated preprocessing script:
```bash
python scripts/preprocess_recording.py /path/to/data --output preprocessed/
```

### scripts/run_sorting.py
Run spike sorting:
```bash
python scripts/run_sorting.py preprocessed/ --sorter kilosort4 --output sorting/
```

### scripts/compute_metrics.py
Compute quality metrics and apply curation:
```bash
python scripts/compute_metrics.py sorting/ preprocessed/ --output metrics/ --curation allen
```

### scripts/export_to_phy.py
Export to Phy for manual curation:
```bash
python scripts/export_to_phy.py metrics/analyzer --output phy_export/
```

### assets/analysis_template.py
Complete analysis template. Copy and customize:
```bash
cp assets/analysis_template.py my_analysis.py
# Edit parameters and run
python my_analysis.py
```

### reference/standard_workflow.md
Detailed step-by-step workflow with explanations for each stage.

### reference/api_reference.md
Quick function reference organized by module.

### reference/plotting_guide.md
Comprehensive visualization guide for publication-quality figures.

## Detailed Reference Guides

| Topic | Reference |
|-------|-----------|
| Full workflow | [reference/standard_workflow.md](reference/standard_workflow.md) |
| API reference | [reference/api_reference.md](reference/api_reference.md) |
| Plotting guide | [reference/plotting_guide.md](reference/plotting_guide.md) |
| Preprocessing | [PREPROCESSING.md](PREPROCESSING.md) |
| Spike sorting | [SPIKE_SORTING.md](SPIKE_SORTING.md) |
| Motion correction | [MOTION_CORRECTION.md](MOTION_CORRECTION.md) |
| Quality metrics | [QUALITY_METRICS.md](QUALITY_METRICS.md) |
| Automated curation | [AUTOMATED_CURATION.md](AUTOMATED_CURATION.md) |
| AI-assisted curation | [AI_CURATION.md](AI_CURATION.md) |
| Waveform analysis | [ANALYSIS.md](ANALYSIS.md) |

## Installation

```bash
# Core packages
pip install spikeinterface[full] probeinterface neo

# Spike sorters
pip install kilosort          # Kilosort4 (GPU required)
pip install spykingcircus     # SpykingCircus2 (CPU)
pip install mountainsort5     # Mountainsort5 (CPU)

# Our toolkit
pip install neuropixels-analysis

# Optional: AI curation
pip install anthropic

# Optional: IBL tools
pip install ibl-neuropixel ibllib
```

## Project Structure

```
project/
├── raw_data/
│   └── recording_g0/
│       └── recording_g0_imec0/
│           ├── recording_g0_t0.imec0.ap.bin
│           └── recording_g0_t0.imec0.ap.meta
├── preprocessed/           # Saved preprocessed recording
├── motion/                 # Motion estimation results
├── sorting_output/         # Spike sorter output
├── analyzer/               # SortingAnalyzer (waveforms, metrics)
├── phy_export/             # For manual curation
├── ai_curation/            # AI analysis reports
└── results/
    ├── quality_metrics.csv
    ├── curation_labels.json
    └── output.nwb
```

## Additional Resources

- **SpikeInterface Docs**: https://spikeinterface.readthedocs.io/
- **Neuropixels Tutorial**: https://spikeinterface.readthedocs.io/en/stable/how_to/analyze_neuropixels.html
- **Kilosort4 GitHub**: https://github.com/MouseLand/Kilosort
- **IBL Neuropixel Tools**: https://github.com/int-brain-lab/ibl-neuropixel
- **Allen Institute ecephys**: https://github.com/AllenInstitute/ecephys_spike_sorting
- **Bombcell (Automated QC)**: https://github.com/Julie-Fabre/bombcell
- **SpikeAgent (AI Curation)**: https://github.com/SpikeAgent/SpikeAgent


---


## 📚 Reference Materials


### Plotting_Guide

# Plotting Guide

Comprehensive guide for creating publication-quality visualizations from Neuropixels data.

## Setup

```python
import matplotlib.pyplot as plt
import numpy as np
import spikeinterface.full as si
import spikeinterface.widgets as sw
import neuropixels_analysis as npa

# High-quality settings
plt.rcParams['figure.dpi'] = 150
plt.rcParams['savefig.dpi'] = 300
plt.rcParams['font.size'] = 10
plt.rcParams['font.family'] = 'sans-serif'
```

## Drift and Motion Plots

### Basic Drift Map

```python
# Using npa
npa.plot_drift(recording, output='drift_map.png')

# Using SpikeInterface widgets
from spikeinterface.preprocessing import detect_peaks, localize_peaks

peaks = detect_peaks(recording, method='locally_exclusive')
peak_locations = localize_peaks(recording, peaks, method='center_of_mass')

sw.plot_drift_raster_map(
    peaks=peaks,
    peak_locations=peak_locations,
    recording=recording,
    clim=(-50, 50),
)
plt.savefig('drift_raster.png', bbox_inches='tight')
```

### Motion Estimate Visualization

```python
motion_info = npa.estimate_motion(recording)

fig, axes = plt.subplots(1, 2, figsize=(12, 5))

# Motion over time
ax = axes[0]
for i in range(motion_info['motion'].shape[1]):
    ax.plot(motion_info['temporal_bins'], motion_info['motion'][:, i], alpha=0.5)
ax.set_xlabel('Time (s)')
ax.set_ylabel('Motion (um)')
ax.set_title('Estimated Motion')

# Motion histogram
ax = axes[1]
ax.hist(motion_info['motion'].flatten(), bins=50, edgecolor='black')
ax.set_xlabel('Motion (um)')
ax.set_ylabel('Count')
ax.set_title('Motion Distribution')

plt.tight_layout()
plt.savefig('motion_analysis.png', dpi=300)
```

## Waveform Plots

### Single Unit Waveforms

```python
unit_id = 0

# Basic waveforms
sw.plot_unit_waveforms(analyzer, unit_ids=[unit_id])
plt.savefig(f'unit_{unit_id}_waveforms.png')

# With density map
sw.plot_unit_waveform_density_map(analyzer, unit_ids=[unit_id])
plt.savefig(f'unit_{unit_id}_density.png')
```

### Template Comparison

```python
# Compare multiple units
unit_ids = [0, 1, 2, 3]
sw.plot_unit_templates(analyzer, unit_ids=unit_ids)
plt.savefig('template_comparison.png')
```

### Waveforms on Probe

```python
# Show waveforms spatially on probe
sw.plot_unit_waveforms_on_probe(
    analyzer,
    unit_ids=[unit_id],
    plot_channels=True,
)
plt.savefig(f'unit_{unit_id}_probe.png')
```

## Quality Metrics Visualization

### Metrics Overview

```python
npa.plot_quality_metrics(analyzer, metrics, output='quality_overview.png')
```

### Metrics Distribution

```python
fig, axes = plt.subplots(2, 3, figsize=(12, 8))

metric_names = ['snr', 'isi_violations_ratio', 'presence_ratio',
                'amplitude_cutoff', 'firing_rate', 'amplitude_cv']

for ax, metric in zip(axes.flat, metric_names):
    if metric in metrics.columns:
        values = metrics[metric].dropna()
        ax.hist(values, bins=30, edgecolor='black', alpha=0.7)
        ax.axvline(values.median(), color='red', linestyle='--', label='median')
        ax.set_xlabel(metric)
        ax.set_ylabel('Count')
        ax.legend()

plt.tight_layout()
plt.savefig('metrics_distribution.png', dpi=300)
```

### Metrics Scatter Matrix

```python
import pandas as pd

key_metrics = ['snr', 'isi_violations_ratio', 'presence_ratio', 'firing_rate']
pd.plotting.scatter_matrix(
    metrics[key_metrics],
    figsize=(10, 10),
    alpha=0.5,
    diagonal='hist',
)
plt.savefig('metrics_scatter.png', dpi=300)
```

### Metrics vs Labels

```python
labels_series = pd.Series(labels)

fig, axes = plt.subplots(1, 3, figsize=(12, 4))

for ax, metric in zip(axes, ['snr', 'isi_violations_ratio', 'presence_ratio']):
    for label in ['good', 'mua', 'noise']:
        mask = labels_series == label
        if mask.any():
            ax.hist(metrics.loc[mask.index[mask], metric],
                   alpha=0.5, label=label, bins=20)
    ax.set_xlabel(metric)
    ax.legend()

plt.tight_layout()
plt.savefig('metrics_by_label.png', dpi=300)
```

## Correlogram Plots

### Autocorrelogram

```python
sw.plot_autocorrelograms(
    analyzer,
    unit_ids=[unit_id],
    window_ms=50,
    bin_ms=1,
)
plt.savefig(f'unit_{unit_id}_acg.png')
```

### Cross-correlograms

```python
unit_pairs = [(0, 1), (0, 2), (1, 2)]
sw.plot_crosscorrelograms(
    analyzer,
    unit_pairs=unit_pairs,
    window_ms=50,
    bin_ms=1,
)
plt.savefig('crosscorrelograms.png')
```

### Correlogram Matrix

```python
sw.plot_autocorrelograms(
    analyzer,
    unit_ids=analyzer.sorting.unit_ids[:10],  # First 10 units
)
plt.savefig('acg_matrix.png')
```

## Spike Train Plots

### Raster Plot

```python
sw.plot_rasters(
    sorting,
    time_range=(0, 30),  # First 30 seconds
    unit_ids=unit_ids[:5],
)
plt.savefig('raster.png')
```

### Firing Rate Over Time

```python
unit_id = 0
spike_train = sorting.get_unit_spike_train(unit_id)
fs = recording.get_sampling_frequency()
times = spike_train / fs

# Compute firing rate histogram
bin_width = 1.0  # seconds
bins = np.arange(0, recording.get_total_duration(), bin_width)
hist, _ = np.histogram(times, bins=bins)
firing_rate = hist / bin_width

plt.figure(figsize=(12, 3))
plt.bar(bins[:-1], firing_rate, width=bin_width, edgecolor='none')
plt.xlabel('Time (s)')
plt.ylabel('Firing rate (Hz)')
plt.title(f'Unit {unit_id} firing rate')
plt.savefig(f'unit_{unit_id}_firing_rate.png', dpi=300)
```

## Probe and Location Plots

### Probe Layout

```python
sw.plot_probe_map(recording, with_channel_ids=True)
plt.savefig('probe_layout.png')
```

### Unit Locations on Probe

```python
sw.plot_unit_locations(analyzer, with_channel_ids=True)
plt.savefig('unit_locations.png')
```

### Spike Locations

```python
sw.plot_spike_locations(analyzer, unit_ids=[unit_id])
plt.savefig(f'unit_{unit_id}_spike_locations.png')
```

## Amplitude Plots

### Amplitudes Over Time

```python
sw.plot_amplitudes(
    analyzer,
    unit_ids=[unit_id],
    plot_histograms=True,
)
plt.savefig(f'unit_{unit_id}_amplitudes.png')
```

### Amplitude Distribution

```python
amplitudes = analyzer.get_extension('spike_amplitudes').get_data()
spike_vector = sorting.to_spike_vector()
unit_idx = list(sorting.unit_ids).index(unit_id)
unit_mask = spike_vector['unit_index'] == unit_idx
unit_amps = amplitudes[unit_mask]

fig, ax = plt.subplots(figsize=(6, 4))
ax.hist(unit_amps, bins=50, edgecolor='black', alpha=0.7)
ax.axvline(np.median(unit_amps), color='red', linestyle='--', label='median')
ax.set_xlabel('Amplitude (uV)')
ax.set_ylabel('Count')
ax.set_title(f'Unit {unit_id} Amplitude Distribution')
ax.legend()
plt.savefig(f'unit_{unit_id}_amp_dist.png', dpi=300)
```

## ISI Plots

### ISI Histogram

```python
sw.plot_isi_distribution(
    analyzer,
    unit_ids=[unit_id],
    window_ms=100,
    bin_ms=1,
)
plt.savefig(f'unit_{unit_id}_isi.png')
```

### ISI with Refractory Markers

```python
spike_train = sorting.get_unit_spike_train(unit_id)
fs = recording.get_sampling_frequency()
isis = np.diff(spike_train) / fs * 1000  # ms

fig, ax = plt.subplots(figsize=(8, 4))
ax.hist(isis[isis < 100], bins=100, edgecolor='black', alpha=0.7)
ax.axvline(1.5, color='red', linestyle='--', label='1.5ms refractory')
ax.axvline(3.0, color='orange', linestyle='--', label='3ms threshold')
ax.set_xlabel('ISI (ms)')
ax.set_ylabel('Count')
ax.set_title(f'Unit {unit_id} ISI Distribution')
ax.legend()
plt.savefig(f'unit_{unit_id}_isi_detailed.png', dpi=300)
```

## Summary Plots

### Unit Summary Panel

```python
npa.plot_unit_summary(analyzer, unit_id, output=f'unit_{unit_id}_summary.png')
```

### Manual Multi-Panel Summary

```python
fig = plt.figure(figsize=(16, 12))

# Waveforms
ax1 = fig.add_subplot(2, 3, 1)
wfs = analyzer.get_extension('waveforms').get_waveforms(unit_id)
for i in range(min(50, wfs.shape[0])):
    ax1.plot(wfs[i, :, 0], 'k', alpha=0.1, linewidth=0.5)
template = wfs.mean(axis=0)[:, 0]
ax1.plot(template, 'b', linewidth=2)
ax1.set_title('Waveforms')

# Template
ax2 = fig.add_subplot(2, 3, 2)
templates_ext = analyzer.get_extension('templates')
template = templates_ext.get_unit_template(unit_id, operator='average')
template_std = templates_ext.get_unit_template(unit_id, operator='std')
x = range(template.shape[0])
ax2.plot(x, template[:, 0], 'b', linewidth=2)
ax2.fill_between(x, template[:, 0] - template_std[:, 0],
                 template[:, 0] + template_std[:, 0], alpha=0.3)
ax2.set_title('Template')

# Autocorrelogram
ax3 = fig.add_subplot(2, 3, 3)
correlograms = analyzer.get_extension('correlograms')
ccg, bins = correlograms.get_data()
unit_idx = list(sorting.unit_ids).index(unit_id)
ax3.bar(bins[:-1], ccg[unit_idx, unit_idx, :], width=bins[1]-bins[0], color='gray')
ax3.axvline(0, color='r', linestyle='--', alpha=0.5)
ax3.set_title('Autocorrelogram')

# Amplitudes
ax4 = fig.add_subplot(2, 3, 4)
amps_ext = analyzer.get_extension('spike_amplitudes')
amps = amps_ext.get_data()
spike_vector = sorting.to_spike_vector()
unit_mask = spike_vector['unit_index'] == unit_idx
unit_times = spike_vector['sample_index'][unit_mask] / fs
unit_amps = amps[unit_mask]
ax4.scatter(unit_times, unit_amps, s=1, alpha=0.3)
ax4.set_xlabel('Time (s)')
ax4.set_ylabel('Amplitude')
ax4.set_title('Amplitudes')

# ISI
ax5 = fig.add_subplot(2, 3, 5)
isis = np.diff(sorting.get_unit_spike_train(unit_id)) / fs * 1000
ax5.hist(isis[isis < 100], bins=50, color='gray', edgecolor='black')
ax5.axvline(1.5, color='r', linestyle='--')
ax5.set_xlabel('ISI (ms)')
ax5.set_title('ISI Distribution')

# Metrics
ax6 = fig.add_subplot(2, 3, 6)
unit_metrics = metrics.loc[unit_id]
text_lines = [f"{k}: {v:.4f}" for k, v in unit_metrics.items() if not np.isnan(v)]
ax6.text(0.1, 0.9, '\n'.join(text_lines[:8]), transform=ax6.transAxes,
         verticalalignment='top', fontsize=10, family='monospace')
ax6.axis('off')
ax6.set_title('Metrics')

plt.tight_layout()
plt.savefig(f'unit_{unit_id}_full_summary.png', dpi=300)
```

## Publication-Quality Settings

### Figure Sizes

```python
# Single column (3.5 inches)
fig, ax = plt.subplots(figsize=(3.5, 3))

# Double column (7 inches)
fig, ax = plt.subplots(figsize=(7, 4))

# Full page
fig, ax = plt.subplots(figsize=(7, 9))
```

### Font Settings

```python
plt.rcParams.update({
    'font.size': 8,
    'axes.titlesize': 9,
    'axes.labelsize': 8,
    'xtick.labelsize': 7,
    'ytick.labelsize': 7,
    'legend.fontsize': 7,
    'font.family': 'Arial',
})
```

### Export Settings

```python
# For publications
plt.savefig('figure.pdf', format='pdf', bbox_inches='tight')
plt.savefig('figure.svg', format='svg', bbox_inches='tight')

# High-res PNG
plt.savefig('figure.png', dpi=600, bbox_inches='tight', facecolor='white')
```

### Color Palettes

```python
# Colorblind-friendly
colors = ['#0072B2', '#E69F00', '#009E73', '#CC79A7', '#F0E442']

# For good/mua/noise
label_colors = {'good': '#2ecc71', 'mua': '#f39c12', 'noise': '#e74c3c'}
```




### Api_Reference

# API Reference

Quick reference for neuropixels_analysis functions organized by module.

## Core Module

### load_recording

```python
npa.load_recording(
    path: str,
    format: str = 'auto',  # 'spikeglx', 'openephys', 'nwb'
    stream_id: str = None,  # e.g., 'imec0.ap'
) -> Recording
```

Load Neuropixels recording from various formats.

### run_pipeline

```python
npa.run_pipeline(
    recording: Recording,
    output_dir: str,
    sorter: str = 'kilosort4',
    preprocess: bool = True,
    correct_motion: bool = True,
    postprocess: bool = True,
    curate: bool = True,
    curation_method: str = 'allen',
) -> dict
```

Run complete analysis pipeline. Returns dictionary with all results.

## Preprocessing Module

### preprocess

```python
npa.preprocess(
    recording: Recording,
    freq_min: float = 300,
    freq_max: float = 6000,
    phase_shift: bool = True,
    common_ref: bool = True,
    bad_channel_detection: bool = True,
) -> Recording
```

Apply standard preprocessing chain.

### detect_bad_channels

```python
npa.detect_bad_channels(
    recording: Recording,
    method: str = 'coherence+psd',
    **kwargs,
) -> list
```

Detect and return list of bad channel IDs.

### apply_filters

```python
npa.apply_filters(
    recording: Recording,
    freq_min: float = 300,
    freq_max: float = 6000,
    filter_type: str = 'bandpass',
) -> Recording
```

Apply frequency filters.

### common_reference

```python
npa.common_reference(
    recording: Recording,
    operator: str = 'median',
    reference: str = 'global',
) -> Recording
```

Apply common reference (CMR/CAR).

## Motion Module

### check_drift

```python
npa.check_drift(
    recording: Recording,
    plot: bool = True,
    output: str = None,
) -> dict
```

Check recording for drift. Returns drift statistics.

### estimate_motion

```python
npa.estimate_motion(
    recording: Recording,
    preset: str = 'kilosort_like',
    **kwargs,
) -> dict
```

Estimate motion without applying correction.

### correct_motion

```python
npa.correct_motion(
    recording: Recording,
    preset: str = 'nonrigid_accurate',
    folder: str = None,
    **kwargs,
) -> Recording
```

Apply motion correction.

**Presets:**
- `'kilosort_like'`: Fast, rigid correction
- `'nonrigid_accurate'`: Slower, better for severe drift
- `'nonrigid_fast_and_accurate'`: Balanced option

## Sorting Module

### run_sorting

```python
npa.run_sorting(
    recording: Recording,
    sorter: str = 'kilosort4',
    output_folder: str = None,
    sorter_params: dict = None,
    **kwargs,
) -> Sorting
```

Run spike sorter.

**Supported sorters:**
- `'kilosort4'`: GPU-based, recommended
- `'kilosort3'`: Legacy, requires MATLAB
- `'spykingcircus2'`: CPU-based alternative
- `'mountainsort5'`: Fast, good for short recordings

### compare_sorters

```python
npa.compare_sorters(
    sortings: list,
    delta_time: float = 0.4,  # ms
    match_score: float = 0.5,
) -> Comparison
```

Compare results from multiple sorters.

## Postprocessing Module

### create_analyzer

```python
npa.create_analyzer(
    sorting: Sorting,
    recording: Recording,
    output_folder: str = None,
    sparse: bool = True,
) -> SortingAnalyzer
```

Create SortingAnalyzer for postprocessing.

### postprocess

```python
npa.postprocess(
    sorting: Sorting,
    recording: Recording,
    output_folder: str = None,
    compute_all: bool = True,
    n_jobs: int = -1,
) -> tuple[SortingAnalyzer, DataFrame]
```

Full postprocessing. Returns (analyzer, metrics).

### compute_quality_metrics

```python
npa.compute_quality_metrics(
    analyzer: SortingAnalyzer,
    metric_names: list = None,  # None = all
    **kwargs,
) -> DataFrame
```

Compute quality metrics for all units.

**Available metrics:**
- `snr`: Signal-to-noise ratio
- `isi_violations_ratio`: ISI violations
- `presence_ratio`: Recording presence
- `amplitude_cutoff`: Amplitude distribution cutoff
- `firing_rate`: Average firing rate
- `amplitude_cv`: Amplitude coefficient of variation
- `sliding_rp_violation`: Sliding window refractory violations
- `d_prime`: Isolation quality
- `nearest_neighbor`: Nearest-neighbor overlap

## Curation Module

### curate

```python
npa.curate(
    metrics: DataFrame,
    method: str = 'allen',  # 'allen', 'ibl', 'strict', 'custom'
    **thresholds,
) -> dict
```

Apply automated curation. Returns {unit_id: label}.

### auto_classify

```python
npa.auto_classify(
    metrics: DataFrame,
    snr_threshold: float = 5.0,
    isi_threshold: float = 0.01,
    presence_threshold: float = 0.9,
) -> dict
```

Classify units based on custom thresholds.

### filter_units

```python
npa.filter_units(
    sorting: Sorting,
    labels: dict,
    keep: list = ['good'],
) -> Sorting
```

Filter sorting to keep only specified labels.

## AI Curation Module

### generate_unit_report

```python
npa.generate_unit_report(
    analyzer: SortingAnalyzer,
    unit_id: int,
    output_dir: str = None,
    figsize: tuple = (16, 12),
) -> dict
```

Generate visual report for AI analysis.

Returns:
- `'image_path'`: Path to saved figure
- `'image_base64'`: Base64 encoded image
- `'metrics'`: Quality metrics dict
- `'unit_id'`: Unit ID

### analyze_unit_visually

```python
npa.analyze_unit_visually(
    analyzer: SortingAnalyzer,
    unit_id: int,
    api_client: Any = None,
    model: str = 'claude-3-5-sonnet-20241022',
    task: str = 'quality_assessment',
    custom_prompt: str = None,
) -> dict
```

Analyze unit using vision-language model.

**Tasks:**
- `'quality_assessment'`: Classify as good/mua/noise
- `'merge_candidate'`: Check if units should merge
- `'drift_assessment'`: Assess motion/drift

### batch_visual_curation

```python
npa.batch_visual_curation(
    analyzer: SortingAnalyzer,
    unit_ids: list = None,
    api_client: Any = None,
    model: str = 'claude-3-5-sonnet-20241022',
    output_dir: str = None,
    progress_callback: callable = None,
) -> dict
```

Run visual curation on multiple units.

### CurationSession

```python
session = npa.CurationSession.create(
    analyzer: SortingAnalyzer,
    output_dir: str,
    session_id: str = None,
    unit_ids: list = None,
    sort_by_confidence: bool = True,
)

# Navigation
session.current_unit() -> UnitCuration
session.next_unit() -> UnitCuration
session.prev_unit() -> UnitCuration
session.go_to_unit(unit_id: int) -> UnitCuration

# Decisions
session.set_decision(unit_id, decision, notes='')
session.set_ai_classification(unit_id, classification)

# Export
session.get_final_labels() -> dict
session.export_decisions(output_path) -> DataFrame
session.get_summary() -> dict

# Persistence
session.save()
session = npa.CurationSession.load(session_dir)
```

## Visualization Module

### plot_drift

```python
npa.plot_drift(
    recording: Recording,
    motion: dict = None,
    output: str = None,
    figsize: tuple = (12, 8),
)
```

Plot drift/motion map.

### plot_quality_metrics

```python
npa.plot_quality_metrics(
    analyzer: SortingAnalyzer,
    metrics: DataFrame = None,
    output: str = None,
)
```

Plot quality metrics overview.

### plot_unit_summary

```python
npa.plot_unit_summary(
    analyzer: SortingAnalyzer,
    unit_id: int,
    output: str = None,
)
```

Plot comprehensive unit summary.

## SpikeInterface Integration

All neuropixels_analysis functions work with SpikeInterface objects:

```python
import spikeinterface.full as si
import neuropixels_analysis as npa

# SpikeInterface recording works with npa functions
recording = si.read_spikeglx('/path/')
rec = npa.preprocess(recording)

# Access SpikeInterface directly for advanced usage
rec_filtered = si.bandpass_filter(recording, freq_min=300, freq_max=6000)
```

## Common Parameters

### Recording parameters
- `freq_min`: Highpass cutoff (Hz)
- `freq_max`: Lowpass cutoff (Hz)
- `n_jobs`: Parallel jobs (-1 = all cores)

### Sorting parameters
- `output_folder`: Where to save results
- `sorter_params`: Dict of sorter-specific params

### Quality metric thresholds
- `snr_threshold`: SNR cutoff (typically 5)
- `isi_threshold`: ISI violations cutoff (typically 0.01)
- `presence_threshold`: Presence ratio cutoff (typically 0.9)




### Standard_Workflow

# Standard Neuropixels Analysis Workflow

Complete step-by-step guide for analyzing Neuropixels recordings from raw data to curated units.

## Overview

This reference documents the complete analysis pipeline:

```
Raw Recording → Preprocessing → Motion Correction → Spike Sorting →
Postprocessing → Quality Metrics → Curation → Export
```

## 1. Data Loading

### Supported Formats

```python
import spikeinterface.full as si
import neuropixels_analysis as npa

# SpikeGLX (most common)
recording = si.read_spikeglx('/path/to/run/', stream_id='imec0.ap')

# Open Ephys
recording = si.read_openephys('/path/to/experiment/')

# NWB format
recording = si.read_nwb('/path/to/file.nwb')

# Or use our convenience wrapper
recording = npa.load_recording('/path/to/data/', format='spikeglx')
```

### Verify Recording Properties

```python
# Basic properties
print(f"Channels: {recording.get_num_channels()}")
print(f"Duration: {recording.get_total_duration():.1f}s")
print(f"Sampling rate: {recording.get_sampling_frequency()}Hz")

# Probe geometry
print(f"Probe: {recording.get_probe().name}")

# Channel locations
locations = recording.get_channel_locations()
```

## 2. Preprocessing

### Standard Preprocessing Chain

```python
# Option 1: Full pipeline (recommended)
rec_preprocessed = npa.preprocess(recording)

# Option 2: Step-by-step control
rec = si.bandpass_filter(recording, freq_min=300, freq_max=6000)
rec = si.phase_shift(rec)  # Correct ADC phase
bad_channels = si.detect_bad_channels(rec)
rec = rec.remove_channels(bad_channels)
rec = si.common_reference(rec, operator='median')
rec_preprocessed = rec
```

### IBL-Style Destriping

For recordings with strong artifacts:

```python
from ibldsp.voltage import decompress_destripe_cbin

# IBL destriping (very effective)
rec = si.highpass_filter(recording, freq_min=400)
rec = si.phase_shift(rec)
rec = si.highpass_spatial_filter(rec)  # Destriping
rec = si.common_reference(rec, reference='global', operator='median')
```

### Save Preprocessed Data

```python
# Save for reuse (speeds up iteration)
rec_preprocessed.save(folder='preprocessed/', n_jobs=4)
```

## 3. Motion/Drift Correction

### Check if Correction Needed

```python
# Estimate motion
motion_info = npa.estimate_motion(rec_preprocessed, preset='kilosort_like')

# Visualize drift
npa.plot_drift(rec_preprocessed, motion_info, output='drift_map.png')

# Check magnitude
if motion_info['motion'].max() > 10:  # microns
    print("Significant drift detected - correction recommended")
```

### Apply Correction

```python
# DREDge-based correction (default)
rec_corrected = npa.correct_motion(
    rec_preprocessed,
    preset='nonrigid_accurate',  # or 'kilosort_like' for speed
)

# Or full control
from spikeinterface.preprocessing import correct_motion

rec_corrected = correct_motion(
    rec_preprocessed,
    preset='nonrigid_accurate',
    folder='motion_output/',
    output_motion=True,
)
```

## 4. Spike Sorting

### Recommended: Kilosort4

```python
# Run Kilosort4 (requires GPU)
sorting = npa.run_sorting(
    rec_corrected,
    sorter='kilosort4',
    output_folder='sorting_KS4/',
)

# With custom parameters
sorting = npa.run_sorting(
    rec_corrected,
    sorter='kilosort4',
    output_folder='sorting_KS4/',
    sorter_params={
        'batch_size': 30000,
        'nblocks': 5,  # For nonrigid drift
        'Th_learned': 8,  # Detection threshold
    },
)
```

### Alternative Sorters

```python
# SpykingCircus2 (CPU-based)
sorting = npa.run_sorting(rec_corrected, sorter='spykingcircus2')

# Mountainsort5 (fast, good for short recordings)
sorting = npa.run_sorting(rec_corrected, sorter='mountainsort5')
```

### Compare Multiple Sorters

```python
# Run multiple sorters
sortings = {}
for sorter in ['kilosort4', 'spykingcircus2']:
    sortings[sorter] = npa.run_sorting(rec_corrected, sorter=sorter)

# Compare results
comparison = npa.compare_sorters(list(sortings.values()))
agreement_matrix = comparison.get_agreement_matrix()
```

## 5. Postprocessing

### Create Analyzer

```python
# Create sorting analyzer (central object for all postprocessing)
analyzer = npa.create_analyzer(
    sorting,
    rec_corrected,
    output_folder='analyzer/',
)

# Compute all standard extensions
analyzer = npa.postprocess(
    sorting,
    rec_corrected,
    output_folder='analyzer/',
    compute_all=True,  # Waveforms, templates, metrics, etc.
)
```

### Compute Individual Extensions

```python
# Waveforms
analyzer.compute('waveforms', ms_before=1.0, ms_after=2.0, max_spikes_per_unit=500)

# Templates
analyzer.compute('templates', operators=['average', 'std'])

# Spike amplitudes
analyzer.compute('spike_amplitudes')

# Correlograms
analyzer.compute('correlograms', window_ms=50.0, bin_ms=1.0)

# Unit locations
analyzer.compute('unit_locations', method='monopolar_triangulation')

# Spike locations
analyzer.compute('spike_locations', method='center_of_mass')
```

## 6. Quality Metrics

### Compute All Metrics

```python
# Compute comprehensive metrics
metrics = npa.compute_quality_metrics(
    analyzer,
    metric_names=[
        'snr',
        'isi_violations_ratio',
        'presence_ratio',
        'amplitude_cutoff',
        'firing_rate',
        'amplitude_cv',
        'sliding_rp_violation',
        'd_prime',
        'nearest_neighbor',
    ],
)

# View metrics
print(metrics.head())
```

### Key Metrics Explained

| Metric | Good Value | Description |
|--------|------------|-------------|
| `snr` | > 5 | Signal-to-noise ratio |
| `isi_violations_ratio` | < 0.01 | Refractory period violations |
| `presence_ratio` | > 0.9 | Fraction of recording with spikes |
| `amplitude_cutoff` | < 0.1 | Estimated missed spikes |
| `firing_rate` | > 0.1 Hz | Average firing rate |

## 7. Curation

### Automated Curation

```python
# Allen Institute criteria
labels = npa.curate(metrics, method='allen')

# IBL criteria
labels = npa.curate(metrics, method='ibl')

# Custom thresholds
labels = npa.curate(
    metrics,
    snr_threshold=5,
    isi_violations_threshold=0.01,
    presence_threshold=0.9,
)
```

### AI-Assisted Curation

```python
from anthropic import Anthropic

# Setup API
client = Anthropic()

# Visual analysis for uncertain units
uncertain = metrics.query('snr > 3 and snr < 8').index.tolist()

for unit_id in uncertain:
    result = npa.analyze_unit_visually(analyzer, unit_id, api_client=client)
    labels[unit_id] = result['classification']
```

### Interactive Curation Session

```python
# Create session
session = npa.CurationSession.create(analyzer, output_dir='curation/')

# Review units
while session.current_unit():
    unit = session.current_unit()
    report = npa.generate_unit_report(analyzer, unit.unit_id)

    # Your decision
    decision = input(f"Unit {unit.unit_id}: ")
    session.set_decision(unit.unit_id, decision)
    session.next_unit()

# Export
labels = session.get_final_labels()
```

## 8. Export Results

### Export to Phy

```python
from spikeinterface.exporters import export_to_phy

export_to_phy(
    analyzer,
    output_folder='phy_export/',
    copy_binary=True,
)
```

### Export to NWB

```python
from spikeinterface.exporters import export_to_nwb

export_to_nwb(
    analyzer,
    nwbfile_path='results.nwb',
    metadata={
        'session_description': 'Neuropixels recording',
        'experimenter': 'Lab Name',
    },
)
```

### Save Quality Summary

```python
# Save metrics CSV
metrics.to_csv('quality_metrics.csv')

# Save labels
import json
with open('curation_labels.json', 'w') as f:
    json.dump(labels, f, indent=2)

# Generate summary report
npa.plot_quality_metrics(analyzer, metrics, output='quality_summary.png')
```

## Full Pipeline Example

```python
import neuropixels_analysis as npa

# Load
recording = npa.load_recording('/data/experiment/', format='spikeglx')

# Preprocess
rec = npa.preprocess(recording)

# Motion correction
rec = npa.correct_motion(rec)

# Sort
sorting = npa.run_sorting(rec, sorter='kilosort4')

# Postprocess
analyzer, metrics = npa.postprocess(sorting, rec)

# Curate
labels = npa.curate(metrics, method='allen')

# Export good units
good_units = [uid for uid, label in labels.items() if label == 'good']
print(f"Good units: {len(good_units)}/{len(labels)}")
```

## Tips for Success

1. **Always visualize drift** before deciding on motion correction
2. **Save preprocessed data** to avoid recomputing
3. **Compare multiple sorters** for critical experiments
4. **Review uncertain units manually** - don't trust automated curation blindly
5. **Document your parameters** for reproducibility
6. **Use GPU** for Kilosort4 (10-50x faster than CPU alternatives)




---

## 🚀 Usage

**Reference this template:** `@skill-neuropixels-analysis.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
