# https-assdaqgeneratorpro.com-

'use client'

import React, { useState, useRef } from 'react';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Card, CardContent } from '@/components/ui/card';
import { Switch } from '@/components/ui/switch';
import { Download, UploadCloud, Eye } from 'lucide-react';

export default function ASSDAQGeneratorPro() {
  const [image, setImage] = useState<string | null>(null);
  const [watermark, setWatermark] = useState('white');
  const [opacity, setOpacity] = useState(0.5);
  const [scale, setScale] = useState(1);
  const [darkMode, setDarkMode] = useState(false);
  const canvasRef = useRef<HTMLCanvasElement>(null);

  const handleUpload = (e: React.ChangeEvent<HTMLInputElement>) => {
    const file = e.target.files?.[0];
    if (file) {
      const reader = new FileReader();
      reader.onload = (e) => setImage(e.target?.result as string);
      reader.readAsDataURL(file);
    }
  };

  const drawCanvas = () => {
    if (!canvasRef.current || !image) return;

    const canvas = canvasRef.current;
    const ctx = canvas.getContext('2d');
    if (!ctx) return;

    const img = new Image();
    img.onload = () => {
      canvas.width = img.width;
      canvas.height = img.height;
      ctx.drawImage(img, 0, 0);

      const watermarkImg = new Image();
      watermarkImg.src = `/logos/${watermark}.png`;
      watermarkImg.onload = () => {
        const wmWidth = watermarkImg.width * scale;
        const wmHeight = watermarkImg.height * scale;
        ctx.globalAlpha = opacity;
        ctx.drawImage(
          watermarkImg,
          canvas.width - wmWidth - 20,
          canvas.height - wmHeight - 20,
          wmWidth,
          wmHeight
        );
        ctx.globalAlpha = 1.0;
      };
    };
    img.src = image;
  };

  const downloadImage = () => {
    drawCanvas();
    const link = document.createElement('a');
    link.download = 'assdaq.png';
    link.href = canvasRef.current!.toDataURL();
    link.click();
  };

  return (
    <div className={`min-h-screen p-6 ${darkMode ? 'bg-black text-white' : 'bg-gray-50'}`}>
      <div className="flex flex-col md:flex-row gap-6 max-w-6xl mx-auto">
        <Card className="w-full md:w-1/3 p-4">
          <CardContent>
            <h2 className="text-xl font-bold mb-4">Upload Image</h2>
            <Input type="file" accept="image/*" onChange={handleUpload} />
            <h3 className="mt-4 font-semibold">Select Watermark</h3>
            <div className="flex gap-4 mt-2">
              <Button variant={watermark === 'white' ? 'default' : 'outline'} onClick={() => setWatermark('white')}>
                WHITE
              </Button>
              <Button variant={watermark === 'black' ? 'default' : 'outline'} onClick={() => setWatermark('black')}>
                BLACK
              </Button>
            </div>
            <div className="mt-4">
              <label>Opacity: {opacity}</label>
              <input type="range" min="0" max="1" step="0.05" value={opacity} onChange={(e) => setOpacity(parseFloat(e.target.value))} />
            </div>
            <div className="mt-4">
              <label>Scale: {scale}</label>
              <input type="range" min="0.2" max="2" step="0.1" value={scale} onChange={(e) => setScale(parseFloat(e.target.value))} />
            </div>
            <div className="mt-4 flex items-center gap-2">
              <Switch checked={darkMode} onCheckedChange={setDarkMode} />
              <span>Dark Mode</span>
            </div>
          </CardContent>
        </Card>

        <Card className="w-full md:w-2/3 p-4">
          <CardContent>
            <h2 className="text-xl font-bold mb-4">Preview</h2>
            {image ? (
              <div>
                <canvas ref={canvasRef} className="w-full border shadow-md" />
                <div className="mt-4 flex gap-4">
                  <Button onClick={drawCanvas}><Eye className="w-4 h-4 mr-2" /> Preview</Button>
                  <Button onClick={downloadImage}><Download className="w-4 h-4 mr-2" /> Download</Button>
                </div>
              </div>
            ) : (
              <p className="text-gray-500">Upload an image to get started.</p>
            )}
          </CardContent>
        </Card>
      </div>

      <div className="mt-8 text-center">
        <p className="text-sm text-muted-foreground">
          Powered by $ASSDAQ | CA: <code className="bg-gray-200 px-1 py-0.5 rounded">0x...xXxopump</code>
        </p>
        <Button variant="link" className="text-xs" onClick={() => navigator.clipboard.writeText('0x7Tx8qTXSakpFasFjd2tPG09n2uy1tEuKYz7gXxopump')}>
          Copy Contract Address
        </Button>
      </div>
    </div>
  );
}
