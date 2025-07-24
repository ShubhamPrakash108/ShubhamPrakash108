<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Shubham Prakash</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background: #0a0a0a;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            overflow: hidden;
        }
        
        canvas {
            background: #000;
        }
    </style>
</head>
<body>
    <canvas id="nameCanvas" width="1000" height="400"></canvas>

    <script>
        const canvas = document.getElementById('nameCanvas');
        const ctx = canvas.getContext('2d');
        
        let time = 0;
        
        // Letter definitions using path points
        const letters = {
            'S': [
                [[0.8, 0], [0.2, 0], [0, 0.2], [0, 0.4], [0.4, 0.5], [0.8, 0.6], [1, 0.8], [1, 1], [0.2, 1], [0, 0.8]]
            ],
            'H': [
                [[0, 0], [0, 1]], [[0, 0.5], [1, 0.5]], [[1, 0], [1, 1]]
            ],
            'U': [
                [[0, 0], [0, 0.8], [0.2, 1], [0.8, 1], [1, 0.8], [1, 0]]
            ],
            'B': [
                [[0, 0], [0, 1]], [[0, 0], [0.6, 0], [0.8, 0.2], [0.6, 0.5], [0, 0.5]], [[0, 0.5], [0.7, 0.5], [1, 0.7], [1, 0.9], [0.7, 1], [0, 1]]
            ],
            'A': [
                [[0, 1], [0.5, 0], [1, 1]], [[0.2, 0.6], [0.8, 0.6]]
            ],
            'M': [
                [[0, 1], [0, 0], [0.5, 0.4], [1, 0], [1, 1]]
            ],
            'P': [
                [[0, 1], [0, 0], [0.8, 0], [1, 0.2], [1, 0.4], [0.8, 0.6], [0, 0.6]]
            ],
            'R': [
                [[0, 1], [0, 0], [0.8, 0], [1, 0.2], [1, 0.4], [0.8, 0.6], [0, 0.6]], [[0.4, 0.6], [1, 1]]
            ],
            'K': [
                [[0, 0], [0, 1]], [[0, 0.5], [1, 0]], [[0.3, 0.5], [1, 1]]
            ]
        };
        
        function drawAnimatedName() {
            ctx.fillStyle = 'rgba(0, 0, 0, 0.05)';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            const firstName = 'SHUBHAM';
            const lastName = 'PRAKASH';
            
            // Draw first name
            drawWord(firstName, 50, 80, 80);
            
            // Draw last name
            drawWord(lastName, 100, 220, 80);
        }
        
        function drawWord(word, startX, startY, letterWidth) {
            for (let i = 0; i < word.length; i++) {
                const letter = word[i];
                const x = startX + i * (letterWidth + 20);
                drawLetter(letter, x, startY, letterWidth, 120, i);
            }
        }
        
        function drawLetter(letter, x, y, width, height, letterIndex) {
            if (!letters[letter]) return;
            
            const letterPaths = letters[letter];
            
            letterPaths.forEach((path, pathIndex) => {
                drawAnimatedPath(path, x, y, width, height, letterIndex, pathIndex);
            });
        }
        
        function drawAnimatedPath(path, x, y, width, height, letterIndex, pathIndex) {
            for (let i = 0; i < path.length - 1; i++) {
                const start = path[i];
                const end = path[i + 1];
                
                const startX = x + start[0] * width;
                const startY = y + start[1] * height;
                const endX = x + end[0] * width;
                const endY = y + end[1] * height;
                
                drawFeatheredLine(startX, startY, endX, endY, letterIndex, pathIndex, i);
            }
        }
        
        function drawFeatheredLine(x1, y1, x2, y2, letterIndex, pathIndex, segmentIndex) {
            const segments = 20;
            const dx = (x2 - x1) / segments;
            const dy = (y2 - y1) / segments;
            
            for (let i = 0; i <= segments; i++) {
                const t = i / segments;
                const currentX = x1 + dx * i;
                const currentY = y1 + dy * i;
                
                // Main stroke with wave
                const wave = Math.sin(time * 2 + letterIndex * 0.5 + pathIndex * 0.3 + i * 0.2) * 3;
                const perpX = -dy / Math.sqrt(dx * dx + dy * dy) || 0;
                const perpY = dx / Math.sqrt(dx * dx + dy * dy) || 0;
                
                const waveX = currentX + perpX * wave;
                const waveY = currentY + perpY * wave;
                
                // Draw main point
                ctx.fillStyle = `rgba(255, 255, 255, ${0.8 + Math.sin(time + letterIndex + i * 0.1) * 0.2})`;
                ctx.beginPath();
                ctx.arc(waveX, waveY, 1.5, 0, Math.PI * 2);
                ctx.fill();
                
                // Draw feather-like extensions
                if (i % 3 === 0) {
                    drawFeatherBarbs(waveX, waveY, letterIndex, pathIndex, i);
                }
            }
        }
        
        function drawFeatherBarbs(centerX, centerY, letterIndex, pathIndex, segmentIndex) {
            const numBarbs = 8;
            
            for (let i = 0; i < numBarbs; i++) {
                const angle = (i / numBarbs) * Math.PI * 2;
                const timeOffset = time + letterIndex * 0.7 + pathIndex * 0.4 + segmentIndex * 0.2;
                const length = 15 + Math.sin(timeOffset + i) * 10;
                
                const endX = centerX + Math.cos(angle + timeOffset * 0.5) * length;
                const endY = centerY + Math.sin(angle + timeOffset * 0.5) * length;
                
                // Draw barb
                ctx.strokeStyle = `rgba(255, 255, 255, ${0.3 + Math.sin(timeOffset + i) * 0.2})`;
                ctx.lineWidth = 0.8;
                ctx.beginPath();
                ctx.moveTo(centerX, centerY);
                ctx.lineTo(endX, endY);
                ctx.stroke();
                
                // Draw barbules
                const barbuleCount = 3;
                for (let j = 1; j <= barbuleCount; j++) {
                    const barbuleT = j / barbuleCount;
                    const barbuleX = centerX + (endX - centerX) * barbuleT;
                    const barbuleY = centerY + (endY - centerY) * barbuleT;
                    
                    const barbuleLength = length * (1 - barbuleT) * 0.5;
                    const barbuleAngle = angle + Math.PI * 0.5 + Math.sin(timeOffset + i + j) * 0.5;
                    
                    const barbuleEndX = barbuleX + Math.cos(barbuleAngle) * barbuleLength;
                    const barbuleEndY = barbuleY + Math.sin(barbuleAngle) * barbuleLength;
                    
                    ctx.strokeStyle = `rgba(255, 255, 255, ${0.2 + Math.sin(timeOffset + i + j) * 0.1})`;
                    ctx.lineWidth = 0.5;
                    ctx.beginPath();
                    ctx.moveTo(barbuleX, barbuleY);
                    ctx.lineTo(barbuleEndX, barbuleEndY);
                    ctx.stroke();
                }
            }
        }
        
        function animate() {
            time += 0.03;
            drawAnimatedName();
            requestAnimationFrame(animate);
        }
        
        // Start animation
        animate();
    </script>
</body>
</html>