const fs = require('fs');
const path = require('path');

function getImageFormat(filePath) {
  try {
    // Read the first 12 bytes of the file
    const buffer = fs.readFileSync(filePath, { start: 0, end: 11 });

    // Check for JPEG
    if (buffer[0] === 0xFF && buffer[1] === 0xD8) {
      return 'JPEG';
    }

    // Check for PNG
    if (buffer[0] === 0x89 && buffer[1] === 0x50 && buffer[2] === 0x4E && buffer[3] === 0x47 &&
        buffer[4] === 0x0D && buffer[5] === 0x0A && buffer[6] === 0x1A && buffer[7] === 0x0A) {
      return 'PNG';
    }

    // Check for GIF
    if (buffer.slice(0, 6).toString() === 'GIF87a' || buffer.slice(0, 6).toString() === 'GIF89a') {
      return 'GIF';
    }

    // Check for BMP
    if (buffer.slice(0, 2).toString() === 'BM') {
      return 'BMP';
    }

    // Check for WebP
    if (buffer.slice(0, 4).toString() === 'RIFF' && buffer.slice(8, 12).toString() === 'WEBP') {
      return 'WebP';
    }

    // Check for TIFF
    if ((buffer[0] === 0x49 && buffer[1] === 0x49) || (buffer[0] === 0x4D && buffer[1] === 0x4D)) {
      return 'TIFF';
    }

    // If none match, return unknown
    return 'Unknown format';
  } catch (error) {
    console.error('Error reading file:', error.message);
    return null;
  }
}

// Get file path from command line arguments
const filePath = process.argv[2];

if (!filePath) {
  console.log('Usage: node image_format_analyzer.js <image_file_path>');
  process.exit(1);
}

const format = getImageFormat(filePath);
if (format) {
  console.log(`Image format: ${format}`);
} else {
  console.log('Failed to analyze image format.');
}
