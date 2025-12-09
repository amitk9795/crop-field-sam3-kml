# Crop Field SAM3 KML WebApp

**Automated crop field boundary delineation using SAM3 model with interactive web interface and KML export for precision agriculture.**

## Overview

This webapp integrates the Segment Anything Model 3 (SAM3) with a Leaflet-based web interface to automatically delineate crop field boundaries from satellite imagery. Users can:

- View satellite basemap (supports ESRI, OpenStreetMap, or custom providers)
- Load SAM3-processed field polygons
- Manually refine or draw field boundaries using an interactive drawing interface
- Export field boundaries as KML for use in GIS, agricultural management systems, or precision farming equipment
- Manage multiple fields with metadata and attributes

## Features

✅ **Interactive Mapping**: Leaflet.js-based responsive map interface  
✅ **SAM3 Integration**: Backend API for automated field boundary delineation  
✅ **Vector Drawing**: Leaflet.draw plugin for manual polygon editing/creation  
✅ **KML Export**: Convert polygon boundaries to standard KML format  
✅ **GeoJSON Support**: Native GeoJSON handling for data import/export  
✅ **Responsive Design**: Works on desktop and tablet browsers  
✅ **Docker & GitHub Actions**: Ready-to-deploy with CI/CD pipeline  

## Tech Stack

### Frontend
- **Leaflet.js 1.9.4**: Interactive mapping library
- **Leaflet.draw**: Polygon drawing and editing tools
- **HTML5/CSS3/Vanilla JavaScript**: Core web technologies
- **GeoJSON**: Vector data format
- **KML Export**: Client-side polygon-to-KML conversion

### Backend (Optional - for SAM3 inference)
- **Flask**: Python web framework
- **OpenCV + NumPy**: Image processing
- **Rasterio/GDAL**: GIS data handling
- **Mobile-SAM3**: Lightweight SAM3 model for field delineation
- **Docker**: Containerized deployment

### Deployment
- **GitHub Pages**: Static frontend hosting
- **GitHub Actions**: CI/CD automation
- **Docker**: Backend containerization
- **Cloud Providers**: AWS/GCP/Azure ready

## Project Structure

```
crop-field-sam3-kml/
├── index.html                 # Main web app
├── css/
│   └── style.css             # Styling
├── js/
│   ├── app.js               # Main application logic
│   ├── kml-export.js        # KML conversion utilities
│   └── sam3-client.js       # Backend API client
├── backend/
│   ├── app.py               # Flask application
│   ├── sam3_processor.py    # SAM3 field delineation logic
│   ├── requirements.txt     # Python dependencies
│   └── config.py            # Configuration
├── Dockerfile               # Docker image definition
├── docker-compose.yml       # Local development setup
├── .github/
│   └── workflows/
│       └── deploy.yml       # GitHub Actions pipeline
├── docs/
│   ├── API.md              # Backend API documentation
│   ├── SAM3_METHODOLOGY.md # Field delineation approach
│   └── DEPLOYMENT.md       # Deployment guide
└── README.md               # This file
```

## Quick Start

### Option 1: Standalone Frontend (GitHub Pages)

```bash
# Clone the repository
git clone https://github.com/amitk9795/crop-field-sam3-kml.git
cd crop-field-sam3-kml

# Open in browser (no build step required)
open index.html

# Or serve locally
python -m http.server 8000
# Visit http://localhost:8000
```

### Option 2: Full Stack with Backend (Docker)

```bash
# Clone repository
git clone https://github.com/amitk9795/crop-field-sam3-kml.git
cd crop-field-sam3-kml

# Start with Docker Compose
docker-compose up --build

# Frontend available at: http://localhost:3000
# Backend API at: http://localhost:5000
```

## Usage

### 1. **Load Basemap**
   - Map automatically loads with ESRI World Imagery
   - Adjust zoom/pan to your area of interest (AOI)

### 2. **Upload or Process Satellite Image**
   - Click "Load SAM3 Fields" button
   - Provide satellite image URL or upload local image
   - Backend runs SAM3 to detect field boundaries
   - Polygons appear on map automatically

### 3. **Refine Boundaries (Optional)**
   - Use "Draw" tools to edit polygon vertices
   - Delete incorrect boundaries
   - Create additional fields manually

### 4. **Export as KML**
   - Click "Download KML" button
   - Select fields to export (or all)
   - KML file saves to Downloads folder
   - Ready for GIS/precision agriculture software

## SAM3 Methodology

SAM3-based field delineation works as follows:

```
Satellite Image (RGB or MS)
    ↓
[Preprocessing]
  - Crop to AOI
  - Normalize bands
  - Generate edge map (Canny/LoG)
    ↓
[SAM3 Inference]
  - Use edge map as prompt
  - Generate instance segmentation masks
  - Filter by size (min/max field area)
    ↓
[Post-processing]
  - Simplify boundaries (Douglas-Peucker)
  - Remove slivers and gaps
  - Convert to GeoJSON/KML
    ↓
Field Polygons (GeoJSON)
```

**Key advantages**:
- Works without training data (foundation model)
- Handles varied field shapes and sizes
- Runs efficiently on CPU (Mobile-SAM variant)
- Adaptable to different crop types

## API Documentation

### Frontend → Backend

**POST** `/api/delineate_fields`
```json
{
  "image_url": "https://example.com/satellite.tif",
  "bbox": [82.0, 25.0, 83.0, 26.0],
  "min_field_area": 1000,
  "max_field_area": 500000
}
```

**Response**:
```json
{
  "features": [
    {
      "type": "Feature",
      "geometry": { "type": "Polygon", "coordinates": [...] },
      "properties": { "area_m2": 50000, "id": 1 }
    }
  ]
}
```

## Configuration

### Environment Variables

```bash
# .env file
FLASK_ENV=production
DEBUG=False
SAM3_MODEL_SIZE=mobile  # or 'base' for higher accuracy
MAX_IMAGE_SIZE=2048     # pixels for inference
MIN_FIELD_AREA=500      # m²
MAX_FIELD_AREA=1000000  # m²
```

## Deployment

### GitHub Pages (Frontend Only)

1. Push changes to `main` branch
2. GitHub Actions automatically builds and deploys
3. App available at `https://amitk9795.github.io/crop-field-sam3-kml/`

### Cloud Deployment (Full Stack)

#### AWS EC2
```bash
ssh -i your-key.pem ec2-user@your-instance.amazonaws.com
git clone https://github.com/amitk9795/crop-field-sam3-kml.git
cd crop-field-sam3-kml
docker-compose up -d
```

#### Google Cloud Run
```bash
gcloud run deploy crop-field-sam3 --source .
```

#### Heroku
```bash
git push heroku main
```

See [DEPLOYMENT.md](docs/DEPLOYMENT.md) for detailed instructions.

## Development

### Local Setup

```bash
# Install frontend dependencies (if using build tools)
npm install

# Install backend dependencies
cd backend
pip install -r requirements.txt

# Run locally
python app.py  # Backend on localhost:5000
python -m http.server 8000  # Frontend on localhost:8000
```

### Running Tests

```bash
# Backend tests
cd backend
pytest tests/

# Frontend (browser console for now)
# TODO: Add Jest/Vitest setup
```

## Data Flow

```
User Browser
    ↓
  [Map Interface - Leaflet.js]
    ├─→ Display satellite basemap
    ├─→ Show field polygons
    ├─→ Handle user drawing
    └─→ Manage KML export
    ↓
  [Backend API - Flask]
    ├─→ Receive satellite image
    ├─→ Run SAM3 inference
    ├─→ Process boundaries
    └─→ Return GeoJSON
    ↓
Field Boundaries (KML/GeoJSON)
```

## Performance

| Metric | Target |
|--------|--------|
| Map load time | < 2s |
| SAM3 inference (512px) | 1-3s (GPU), 5-10s (CPU) |
| KML export | < 100ms |
| Polygon rendering (1000 fields) | < 500ms |

## Browser Support

- Chrome/Chromium 90+
- Firefox 88+
- Safari 14+
- Edge 90+

## Troubleshooting

### Map not loading
- Check internet connection
- Verify tile provider URL is correct
- Check browser console for CORS errors

### SAM3 inference timeout
- Reduce image size in settings
- Use `mobile` model variant
- Increase server timeout

### KML export produces empty file
- Ensure at least one polygon is drawn
- Check polygon coordinates are valid
- Verify browser allows downloads

See [docs/FAQ.md](docs/FAQ.md) for more help.

## Citation & References

**SAM3 Model**:
```bibtex
@article{sam2024,
  title={Segment Anything Model 3},
  author={Meta AI},
  journal={arXiv},
  year={2024}
}
```

**Leaflet.js**:
```
Leaflet - a JavaScript library for interactive maps
https://leafletjs.com
```

## Contributing

Contributions welcome! Please:

1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open Pull Request

Please ensure:
- Code follows existing style
- Comments explain complex logic
- Tests pass locally
- Documentation is updated

## License

MIT License - See [LICENSE](LICENSE) file

## Author

**Amit Kumar** (@amitk9795)  
MTech Environmental Engineering  
Graduation: 2025  

## Support

- 📧 Email: your.email@example.com
- 🐛 Issues: [GitHub Issues](https://github.com/amitk9795/crop-field-sam3-kml/issues)
- 💬 Discussions: [GitHub Discussions](https://github.com/amitk9795/crop-field-sam3-kml/discussions)
- 📖 Docs: [Wiki](https://github.com/amitk9795/crop-field-sam3-kml/wiki)

---

**Last Updated**: December 2024  
**Status**: Active Development (Alpha v0.1)
