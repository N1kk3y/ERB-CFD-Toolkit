<p align="center">
  <img src="logo.png" alt="ERB CFD Toolkit logo" width="220">
</p>

<h1 align="center">ERB CFD Toolkit</h1>

<p align="center">
  Desktop toolkit for 2D airfoil preparation and CFD post-processing.<br>
  Used in the aerodynamics workflow of E-Racing Bergamo, 2023-2025.
</p>

| Category | Badges |
|----------|--------|
| License | ![License](https://img.shields.io/badge/license-Non--Commercial-blue) |
| Stack | ![Python](https://img.shields.io/badge/python-3-3776AB?logo=python&logoColor=white) ![PyQt5](https://img.shields.io/badge/GUI-PyQt5-41CD52?logo=qt&logoColor=white) ![pyqtgraph](https://img.shields.io/badge/plots-pyqtgraph-orange) ![ReportLab](https://img.shields.io/badge/reports-ReportLab-lightgrey) |
| Platform | ![Platform](https://img.shields.io/badge/platform-Windows-0078D6) ![Type](https://img.shields.io/badge/type-desktop%20app-555555) |
| Language | ![Language](https://img.shields.io/badge/language-Italian-009246) |
| Status | ![Status](https://img.shields.io/badge/status-completed-brightgreen) ![Maintenance](https://img.shields.io/badge/updates-none%20planned-lightgrey) |
| Project | ![Used by](https://img.shields.io/badge/used%20by-ERB%20(E--Racing%20Bergamo)-004D40) ![Period](https://img.shields.io/badge/used-2023--2025-555555) ![Repo size](https://img.shields.io/github/repo-size/N1kk3y/ERB-Orchestrator---Nicolo-Ongaro) |

| Version | Release |
|---------|---------|
| Final version | ![Version](https://img.shields.io/badge/version-v7.2.0-blue) ![Date](https://img.shields.io/badge/released-15%20March%202026-555555) |
| Final release | ![Release](https://img.shields.io/github/v/release/N1kk3y/ERB-Orchestrator---Nicolo-Ongaro?include_prereleases) |

Used by ERB (E-Racing Bergamo), the Formula Student team of the University of Bergamo, between 2023 and 2025. This is a personal project and not an official repository of the team.

The application groups the small utilities used in the 2D aerodynamic workflow (profile conversion, plotting, Gurney flap modelling, ANSYS report generation) behind a single launcher, so that a profile can be moved from one step to the next without leaving the program.

- **Developer:** Nicolò Ongaro
- **Status:** completed. The project is finished and no further updates are planned.
- **Final version:** v7.2.0 (15 March 2026)
- **Platform:** Windows (the launcher uses Windows-specific calls for opening folders)
- **License:** custom non-commercial license, see [LICENSE](LICENSE)

---

## Download

A ready-to-run Windows executable is available in the [**Releases**](https://github.com/N1kk3y/ERB-Orchestrator---Nicolo-Ongaro/releases/latest) page. No Python installation and no compilation are required: download `ERB.Toolkit.exe` and run it.

- Current and final version: v7.2.0, about 150 MB (156.5 MB).
- Windows only.

## Overview

The launcher (`main.py`) is a PyQt5 application. Each tool is a separate module loaded on demand when its button is clicked. Tools that produce a profile file can send it directly to the Airfoil Plotter.

Typical workflow:

```
CSV airfoil data ──► CSV Converter ──┐
                                     ├──► Airfoil Plotter ──► Gurney Flap ──► profile + Gurney (.txt)
Fusion 360 export ──► Fusion Converter ┘

ANSYS Workbench HTML report + Fluent images ──► Ansys Report ──► PDF
```

## Tools

| Tool | Module | Purpose |
|------|--------|---------|
| CSV Converter | `CSV_Airfoils_Converter.py` | Converts an airfoil CSV into the point-table TXT format used by the other tools |
| Airfoil Plotter | `plotter_v3.py` | Plots a profile, applies rotation and scaling, reports origin and vertical distance |
| Fusion Converter | `Fusion_TXT_converter.py` | Converts a coordinate TXT exported from Fusion 360 into the same TXT format |
| Download Script | `Dowload_Fusion_Script.py` | Saves the bundled Fusion 360 toolkit (ZIP) to a location chosen by the user |
| Gurney Flap | `Gurney_Flap.py` | Adds a parametric Gurney flap to a profile and exports the modified profile |
| Ansys Report | `Ansys_Report.py` | Builds a PDF report from an ANSYS HTML report and the Fluent result images |
| Airfoils Mapper | `AirfoilsWEB.py`, `AirfoilsWEB/` | Embedded web editor for arranging airfoil profiles and flaps and setting the simulation domain |

### CSV Converter

- Reads a CSV containing an `X(mm),Y(mm)` coordinate block, stopping before the `Camber line` section.
- Divides the coordinates by 100 and preserves the original decimal precision plus three digits.
- The user selects whether the profile is **open** or **closed** at the trailing edge. For closed profiles a closing marker line (`1 0`) is appended, which the plotter uses to draw the closing segment.
- Writes `<name>-converted-open.txt` or `<name>-converted-close.txt` next to the input file.
- After conversion, the file can be opened in its folder or sent directly to the Airfoil Plotter.

### Airfoil Plotter

- Loads TXT files in the `Group / Point / X_cord / Y_cord / Z_cord` format (decimal comma accepted).
- Global scale, independent X and Y scale, and rotation angle.
- Splits the profile into upper and lower curves and draws them separately.
- Displays the origin point, the lowest Y value and the vertical distance between them, together with point counts.
- Advanced view: shows the index and coordinates of every point.
- Renders the closing segment for profiles marked as closed.

### Fusion Converter

- Reads comma-separated `x,y,z` coordinates exported from Fusion 360.
- Applies a 0.1 scale factor and inverts the sign of X and Y.
- Removes points in the second half of the list that duplicate points in the first half.
- Writes `<name>_converted-open.txt` or `<name>_converted-close.txt` in the same TXT format used by the plotter.

### Gurney Flap

- The user picks two points on the profile: the trailing-edge point (*Punto 1 TE*) and a second point (*Punto 2*).
- The segment between them is resampled to 50 points and a slider selects how much of it carries the flap.
- Parameters: flap height, extension, and angle (degrees).
- The flap outline can be resampled to a chosen number of points.
- Exports the original profile with the flap geometry inserted, as `<name>_gurney_<angle>deg.txt`.

### Ansys Report

- Inputs: the ANSYS HTML report, the folder containing the Fluent images, and title, author, configuration and notes.
- Extracts the *Design Points* table from the HTML.
- Produces a landscape A4 PDF containing:
  - an information block (author, date, configuration, notes);
  - the Design Points table, with each DP linked to its own page;
  - one page per DP with its parameter row, `velocity.png` and `y+.png` read from `FluentObjects/<dp>/FFF/`, and a link back to the table.
- Progress is shown while the PDF is generated.

### Airfoils Mapper

- Web interface (`AirfoilsWEB/index.html`) shown inside the application through Qt WebEngine.
- Imports airfoil profiles from `.txt` files through the native file dialog.
- Supports a main profile with flap elements attached at the trailing edge.
- Sets the domain dimensions (height, width, ground level, in metres).
- Includes a collapsible panel for ANSYS parameters.

### Launcher

- Settings page with developer information, version and a button to export the technical guide (PDF) to the Desktop.
- Update check against the latest GitHub release; when a newer non-beta release with an `.exe` asset exists, a notice is shown in the footer. Since the project is completed, no further releases are planned.

## Repository structure

```
.
├── main.py                       # Launcher
├── CSV_Airfoils_Converter.py
├── Fusion_TXT_converter.py
├── plotter_v3.py
├── Gurney_Flap.py
├── Ansys_Report.py
├── AirfoilsWEB.py                # Qt wrapper for the web module
├── AirfoilsWEB/                  # Web module (index.html and assets)
├── Dowload_Fusion_Script.py
├── resources/                    # Technical guide (PDF) and Fusion 360 toolkit (ZIP)
├── *.png, icon.ico               # Launcher icons and illustrations
├── LICENSE
└── README.md
```

## Installation

The easiest way to use the toolkit is the prebuilt executable described in [Download](#download).

To run from source instead:

```bash
git clone https://github.com/N1kk3y/ERB-Orchestrator---Nicolo-Ongaro.git
cd ERB-Orchestrator---Nicolo-Ongaro
pip install PyQt5 PyQtWebEngine pyqtgraph numpy pandas beautifulsoup4 reportlab requests packaging
python main.py
```

The repository does not currently include a `requirements.txt`; the list above is derived from the imports in the source files.

## Notes

- The user interface of the launcher and of most tools is in Italian. The Airfoils Mapper interface is in English.
- Folder-opening buttons rely on Windows (`os.startfile`, `explorer`), so those buttons will not work on other systems.
- The Download Script and the guide export read files from the `resources/` folder; they need that folder to be present next to the program (or bundled with it).

## License

Distributed under a custom non-commercial license: personal, educational and non-commercial use is permitted with attribution to Nicolò Ongaro. The software is provided as is, without warranty. This is not an OSI-approved open-source license. Full text in [LICENSE](LICENSE).

## Contact

For collaborations or permissions beyond the license: n.ongaro2@studenti.unibg.it
