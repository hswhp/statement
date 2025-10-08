<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Witness Statement Generator</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
            background-color: #fafafa;
            padding: 16px;
            line-height: 1.5;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            background: white;
            padding: 24px;
            border-radius: 8px;
            box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        }
        h1 {
            font-size: 24px;
            font-weight: 600;
            color: #111;
            margin-bottom: 24px;
        }
        .form-group {
            margin-bottom: 20px;
        }
        label {
            display: block;
            font-size: 14px;
            font-weight: 500;
            margin-bottom: 6px;
            color: #333;
        }
        input[type="text"], 
        input[type="date"], 
        input[type="number"], 
        select,
        textarea {
            width: 100%;
            padding: 10px 12px;
            border: 1px solid #ddd;
            border-radius: 6px;
            font-size: 15px;
            font-family: inherit;
            background: white;
            transition: border-color 0.2s;
        }
        input:focus, 
        select:focus, 
        textarea:focus {
            border-color: #2563eb;
            outline: none;
        }
        textarea {
            min-height: 120px;
            resize: vertical;
        }
        .date-input-group {
            display: flex;
            gap: 8px;
        }
        .date-input-group input {
            flex: 1;
        }
        .today-btn {
            padding: 10px 16px;
            background: #f3f4f6;
            border: 1px solid #ddd;
            border-radius: 6px;
            cursor: pointer;
            font-size: 14px;
            font-weight: 500;
            color: #374151;
            white-space: nowrap;
            transition: all 0.2s;
        }
        .today-btn:hover {
            background: #e5e7eb;
        }
        .today-btn:active {
            background: #d1d5db;
        }
        .age-input-toggle {
            display: flex;
            gap: 8px;
            margin-bottom: 12px;
        }
        .toggle-btn {
            flex: 1;
            padding: 8px;
            background: #f3f4f6;
            border: 1px solid #ddd;
            border-radius: 6px;
            cursor: pointer;
            font-size: 14px;
            font-weight: 500;
            transition: all 0.2s;
        }
        .toggle-btn.active {
            background: #2563eb;
            color: white;
            border-color: #2563eb;
        }
        .age-input-field {
            display: none;
        }
        .age-input-field.active {
            display: block;
        }
        .signature-section {
            margin-top: 32px;
            padding: 20px;
            background-color: #f9fafb;
            border-radius: 8px;
            border: 1px solid #e5e7eb;
        }
        .signature-section h3 {
            font-size: 16px;
            font-weight: 600;
            margin-bottom: 16px;
            color: #111;
        }
        .signature-row {
            display: grid;
            grid-template-columns: 1fr;
            gap: 24px;
        }
        @media (min-width: 768px) {
            .signature-row {
                grid-template-columns: 1fr 1fr;
            }
        }
        .signature-box label {
            font-size: 13px;
            margin-bottom: 8px;
        }
        .signature-canvas-container {
            border: 1px solid #d1d5db;
            background-color: white;
            margin-bottom: 8px;
            border-radius: 6px;
            overflow: hidden;
        }
        .signature-canvas {
            display: block;
            cursor: crosshair;
            width: 100%;
            height: auto;
        }
        .clear-signature {
            background-color: #ef4444;
            color: white;
            border: none;
            padding: 6px 12px;
            border-radius: 6px;
            cursor: pointer;
            font-size: 13px;
            font-weight: 500;
            transition: background-color 0.2s;
        }
        .clear-signature:hover {
            background-color: #dc2626;
        }
        .signature-details {
            margin-top: 12px;
        }
        .signature-details > div {
            margin-bottom: 12px;
        }
        .signature-details label {
            font-size: 13px;
        }
        .generate-btn {
            width: 100%;
            padding: 14px;
            background-color: #2563eb;
            color: white;
            border: none;
            border-radius: 6px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            margin-top: 24px;
            transition: background-color 0.2s;
        }
        .generate-btn:hover {
            background-color: #1d4ed8;
        }
        .generate-btn:active {
            background-color: #1e40af;
        }
        small {
            display: block;
            margin-top: 4px;
            color: #6b7280;
            font-size: 13px;
        }
        @media (max-width: 640px) {
            body {
                padding: 12px;
            }
            .container {
                padding: 20px;
            }
            h1 {
                font-size: 20px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Witness Statement Generator</h1>
        
        <form id="statementForm">
            <div class="form-group">
                <label for="matterOf">In the matter of:</label>
                <input type="text" id="matterOf" name="matterOf" placeholder="Police V" required>
            </div>
            
            <div class="form-group">
                <label for="place">Place:</label>
                <input type="text" id="place" name="place" required>
            </div>
            
            <div class="form-group">
                <label for="date">Date:</label>
                <div class="date-input-group">
                    <input type="date" id="date" name="date" required>
                    <button type="button" class="today-btn" onclick="setToday()">Today</button>
                </div>
            </div>
            
            <div class="form-group">
                <label for="name">Name:</label>
                <input type="text" id="name" name="name" required>
            </div>
            
            <div class="form-group">
                <label>Age or Date of Birth:</label>
                <div class="age-input-toggle">
                    <button type="button" class="toggle-btn active" onclick="toggleAgeInput('age')">Age</button>
                    <button type="button" class="toggle-btn" onclick="toggleAgeInput('dob')">Date of Birth</button>
                </div>
                <div id="ageField" class="age-input-field active">
                    <input type="number" id="age" name="age" min="1" max="120" placeholder="Enter age">
                </div>
                <div id="dobField" class="age-input-field">
                    <input type="text" id="dob" name="dob" placeholder="DD/MM/YYYY">
                    <small>Enter date of birth in DD/MM/YYYY format</small>
                </div>
            </div>
            
            <div class="form-group">
                <label for="statementText">Statement Details:</label>
                <textarea id="statementText" name="statementText" placeholder="Enter your detailed statement here. Leave blank lines to create separate numbered paragraphs." required></textarea>
                <small>Tip: Leave blank lines in your text to automatically create separate numbered paragraphs in the final document.</small>
            </div>
            
            <div class="signature-section">
                <h3>Signatures</h3>
                
                <div class="signature-row">
                    <div class="signature-box">
                        <label>Statement Maker Signature:</label>
                        <div class="signature-canvas-container">
                            <canvas id="statementMakerCanvas" class="signature-canvas" height="120"></canvas>
                        </div>
                        <div class="signature-controls">
                            <button type="button" class="clear-signature" onclick="clearSignature('statementMakerCanvas')">Clear Signature</button>
                        </div>
                        <div class="signature-details">
                            <div>
                                <label for="statementMakerName">Name:</label>
                                <input type="text" id="statementMakerName" name="statementMakerName" readonly>
                            </div>
                            <div>
                                <label for="statementMakerDate">Date:</label>
                                <input type="date" id="statementMakerDate" name="statementMakerDate" required>
                            </div>
                        </div>
                    </div>
                    
                    <div class="signature-box">
                        <label>Witness Signature:</label>
                        <div class="signature-canvas-container">
                            <canvas id="witnessCanvas" class="signature-canvas" height="120"></canvas>
                        </div>
                        <div class="signature-controls">
                            <button type="button" class="clear-signature" onclick="clearSignature('witnessCanvas')">Clear Signature</button>
                        </div>
                        <div class="signature-details">
                            <div>
                                <label for="witnessName">Witness Name:</label>
                                <input type="text" id="witnessName" name="witnessName" required>
                            </div>
                            <div>
                                <label for="witnessRank">Witness Rank:</label>
                                <select id="witnessRank" name="witnessRank" required>
                                    <option value="">Select rank...</option>
                                    <option value="Constable">Constable</option>
                                    <option value="Senior Constable">Senior Constable</option>
                                    <option value="Sergeant">Sergeant</option>
                                    <option value="Inspector">Inspector</option>
                                </select>
                            </div>
                            <div>
                                <label for="witnessDate">Date:</label>
                                <input type="date" id="witnessDate" name="witnessDate" required>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            
            <button type="button" class="generate-btn" onclick="generatePDF()">Generate PDF Statement</button>
        </form>
    </div>

    <script>
        // Initialize signature canvases
        let isDrawing = false;
        let canvases = {};
        let currentAgeInput = 'age'; // Track which age input method is active

        function setToday() {
            const today = new Date().toISOString().split('T')[0];
            document.getElementById('date').value = today;
            document.getElementById('statementMakerDate').value = today;
            document.getElementById('witnessDate').value = today;
        }

        function toggleAgeInput(type) {
            currentAgeInput = type;
            
            // Update toggle buttons
            const buttons = document.querySelectorAll('.toggle-btn');
            buttons.forEach(btn => btn.classList.remove('active'));
            event.target.classList.add('active');
            
            // Show/hide appropriate fields
            document.getElementById('ageField').classList.remove('active');
            document.getElementById('dobField').classList.remove('active');
            
            if (type === 'age') {
                document.getElementById('ageField').classList.add('active');
                document.getElementById('dob').value = '';
            } else {
                document.getElementById('dobField').classList.add('active');
                document.getElementById('age').value = '';
            }
        }

        function calculateAgeFromDOB(dobString) {
            // Parse DD/MM/YYYY format
            const parts = dobString.split('/');
            if (parts.length !== 3) return null;
            
            const day = parseInt(parts[0], 10);
            const month = parseInt(parts[1], 10) - 1; // Month is 0-indexed
            const year = parseInt(parts[2], 10);
            
            if (isNaN(day) || isNaN(month) || isNaN(year)) return null;
            
            const birthDate = new Date(year, month, day);
            const today = new Date();
            
            let age = today.getFullYear() - birthDate.getFullYear();
            const monthDiff = today.getMonth() - birthDate.getMonth();
            
            if (monthDiff < 0 || (monthDiff === 0 && today.getDate() < birthDate.getDate())) {
                age--;
            }
            
            return age;
        }

        // Auto-calculate age from DOB
        document.getElementById('dob').addEventListener('input', function() {
            const dobString = this.value.trim();
            if (dobString.length === 10) { // DD/MM/YYYY = 10 characters
                const age = calculateAgeFromDOB(dobString);
                if (age !== null && age >= 0 && age <= 120) {
                    document.getElementById('age').value = age;
                }
            }
        });

        function initializeCanvas(canvasId) {
            const canvas = document.getElementById(canvasId);
            const ctx = canvas.getContext('2d');
            
            // Set canvas size based on container
            const container = canvas.parentElement;
            const rect = container.getBoundingClientRect();
            canvas.width = rect.width;
            canvas.height = 120;
            
            canvases[canvasId] = { canvas, ctx };
            
            // Set up drawing context
            ctx.strokeStyle = '#000000';
            ctx.lineWidth = 2;
            ctx.lineCap = 'round';
            ctx.lineJoin = 'round';
            
            // Mouse events
            canvas.addEventListener('mousedown', startDrawing);
            canvas.addEventListener('mousemove', draw);
            canvas.addEventListener('mouseup', stopDrawing);
            canvas.addEventListener('mouseout', stopDrawing);
            
            // Touch events for mobile
            canvas.addEventListener('touchstart', handleTouch);
            canvas.addEventListener('touchmove', handleTouch);
            canvas.addEventListener('touchend', stopDrawing);
            
            function startDrawing(e) {
                isDrawing = true;
                const rect = canvas.getBoundingClientRect();
                const x = e.clientX - rect.left;
                const y = e.clientY - rect.top;
                ctx.beginPath();
                ctx.moveTo(x, y);
            }
            
            function draw(e) {
                if (!isDrawing) return;
                const rect = canvas.getBoundingClientRect();
                const x = e.clientX - rect.left;
                const y = e.clientY - rect.top;
                ctx.lineTo(x, y);
                ctx.stroke();
            }
            
            function stopDrawing() {
                if (isDrawing) {
                    ctx.stroke();
                    ctx.beginPath();
                    isDrawing = false;
                }
            }
            
            function handleTouch(e) {
                e.preventDefault();
                const touch = e.touches[0];
                const mouseEvent = new MouseEvent(e.type === 'touchstart' ? 'mousedown' : 
                                                e.type === 'touchmove' ? 'mousemove' : 'mouseup', {
                    clientX: touch.clientX,
                    clientY: touch.clientY
                });
                canvas.dispatchEvent(mouseEvent);
            }
        }

        // Initialize both canvases
        document.addEventListener('DOMContentLoaded', function() {
            initializeCanvas('statementMakerCanvas');
            initializeCanvas('witnessCanvas');
        });

        function clearSignature(canvasId) {
            const { canvas, ctx } = canvases[canvasId];
            ctx.clearRect(0, 0, canvas.width, canvas.height);
        }

        // Auto-fill statement maker name when name field changes
        document.getElementById('name').addEventListener('input', function() {
            document.getElementById('statementMakerName').value = this.value;
        });

        // Set today's date as default for signature dates
        const today = new Date().toISOString().split('T')[0];
        document.getElementById('statementMakerDate').value = today;
        document.getElementById('witnessDate').value = today;

        function generatePDF() {
            // Get form data
            const matterOf = document.getElementById('matterOf').value;
            const place = document.getElementById('place').value;
            const date = new Date(document.getElementById('date').value).toLocaleDateString('en-GB');
            const name = document.getElementById('name').value;
            
            // Get age from appropriate input
            let age;
            if (currentAgeInput === 'age') {
                age = document.getElementById('age').value;
            } else {
                const dobString = document.getElementById('dob').value;
                age = calculateAgeFromDOB(dobString);
                if (age === null) {
                    alert('Please enter a valid date of birth in DD/MM/YYYY format.');
                    return;
                }
            }
            
            const statementText = document.getElementById('statementText').value;
            const statementMakerDate = new Date(document.getElementById('statementMakerDate').value).toLocaleDateString('en-GB');
            const witnessName = document.getElementById('witnessName').value;
            const witnessRank = document.getElementById('witnessRank').value;
            const witnessDate = new Date(document.getElementById('witnessDate').value).toLocaleDateString('en-GB');
            
            // Validate required fields
            if (!matterOf || !place || !date || !name || !age || !statementText || !witnessName || !witnessRank) {
                alert('Please fill in all required fields before generating the PDF.');
                return;
            }

            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();

            function addSignatures(currentY) {
                const bottomMargin = 20;
                const signatureY = 280 - bottomMargin;
                
                // Left side - Witness (moved left with colon)
                doc.setFont(undefined, 'normal');
                const witnessText = 'Witness:';
                const witnessTextWidth = doc.getTextWidth(witnessText);
                doc.text(witnessText, margin - 10, signatureY - 5);
                doc.line(margin - 10 + witnessTextWidth, signatureY - 5, margin - 10 + witnessTextWidth + 50, signatureY - 5);
                
                // Add witness signature if drawn
                const witnessCanvas = document.getElementById('witnessCanvas');
                const isWitnessBlank = isCanvasBlank(witnessCanvas);
                if (!isWitnessBlank) {
                    const witnessSignatureData = witnessCanvas.toDataURL('image/png');
                    doc.addImage(witnessSignatureData, 'PNG', margin - 10 + witnessTextWidth, signatureY - 20, 50, 15);
                }
                
                // Information directly below signature line
                doc.text(witnessName, margin - 10 + witnessTextWidth, signatureY + 3);
                doc.text(witnessRank, margin - 10 + witnessTextWidth, signatureY + 9);
                doc.text(witnessDate, margin - 10 + witnessTextWidth, signatureY + 15);

                // Right side - Statement Maker (moved left with colon)
                const rightSideStart = margin + 90;
                const signatureText = 'Signature:';
                const signatureTextWidth = doc.getTextWidth(signatureText);
                doc.text(signatureText, rightSideStart, signatureY - 5);
                doc.line(rightSideStart + signatureTextWidth, signatureY - 5, rightSideStart + signatureTextWidth + 40, signatureY - 5);
                
                // Add statement maker signature if drawn
                const statementCanvas = document.getElementById('statementMakerCanvas');
                const isStatementBlank = isCanvasBlank(statementCanvas);
                if (!isStatementBlank) {
                    const statementSignatureData = statementCanvas.toDataURL('image/png');
                    doc.addImage(statementSignatureData, 'PNG', rightSideStart + signatureTextWidth, signatureY - 20, 40, 15);
                }
                
                // Information directly below signature line
                doc.text(name, rightSideStart + signatureTextWidth, signatureY + 3);
                doc.text(statementMakerDate, rightSideStart + signatureTextWidth, signatureY + 9);
            }

            function addPageHeader(pageNum) {
                if (pageNum > 1) {
                    doc.setFontSize(10);
                    doc.setFont(undefined, 'bold');
                    const statementLabel = `Statement of:`;
                    const matterLabel = `In the matter of:`;
                    
                    // Calculate total widths for centering
                    const line1Width = doc.getTextWidth(statementLabel) + 1 + doc.getTextWidth(name);
                    const line2Width = doc.getTextWidth(matterLabel) + 1 + doc.getTextWidth(matterOf);
                    
                    // Find the starting position so both labels align at 'S' and 'I'
                    const maxLineWidth = Math.max(line1Width, line2Width);
                    const centerStart = (pageWidth - maxLineWidth) / 2;
                    
                    // Both labels start from the same vertical line (where 'S' is)
                    doc.text(statementLabel, centerStart, 10);
                    doc.text(name, centerStart + doc.getTextWidth(statementLabel) + 1, 10);
                    
                    doc.text(matterLabel, centerStart, 17);
                    doc.text(matterOf, centerStart + doc.getTextWidth(matterLabel) + 1, 17);
                    
                    // Reset font size for content
                    doc.setFontSize(12);
                    doc.setFont(undefined, 'normal');
                }
            }

            let yPosition = 20;
            const margin = 20;
            const pageWidth = doc.internal.pageSize.width;
            const maxWidth = pageWidth - 2 * margin;

            // Title
            doc.setFontSize(16);
            doc.setFont(undefined, 'bold');
            const title = 'STATEMENT OF A WITNESS';
            const titleWidth = doc.getTextWidth(title);
            doc.text(title, (pageWidth - titleWidth) / 2, yPosition);
            yPosition += 15;

            // Slightly thinner black line below title
            doc.setLineWidth(1.2);
            doc.line(margin, yPosition, pageWidth - margin, yPosition);
            doc.setLineWidth(0.2); // Reset line width
            yPosition += 12;

            // Case details box - smaller height
            doc.setFontSize(12);
            doc.setFont(undefined, 'normal');
            const caseDetailsHeight = 20;
            doc.rect(margin, yPosition, maxWidth, caseDetailsHeight);
            yPosition += 5;
            
            doc.setFont(undefined, 'bold');
            doc.text('In the matter of:', margin + 3, yPosition);
            doc.setFont(undefined, 'normal');
            doc.text(matterOf, margin + 65, yPosition);
            yPosition += 5;
            doc.setFont(undefined, 'bold');
            doc.text('Place:', margin + 3, yPosition);
            doc.setFont(undefined, 'normal');
            doc.text(place, margin + 65, yPosition);
            yPosition += 5;
            doc.setFont(undefined, 'bold');
            doc.text('Date:', margin + 3, yPosition);
            doc.setFont(undefined, 'normal');
            doc.text(date, margin + 65, yPosition);
            yPosition += 8;

            // Name box - smaller height
            const nameBoxHeight = 7;
            doc.rect(margin, yPosition, maxWidth, nameBoxHeight);
            yPosition += 5;
            doc.setFont(undefined, 'bold');
            doc.text('Name:', margin + 3, yPosition);
            doc.setFont(undefined, 'normal');
            doc.text(name, margin + 33, yPosition);
            yPosition += 7;

            // STATES header
            doc.setFont(undefined, 'bold');
            doc.text('STATES:', margin, yPosition);
            yPosition += 8;
            doc.setFont(undefined, 'normal');

            // Standard statements with proper indentation and aggressive page filling
            let statementNumber = 1;
            const numberIndent = 5; // Space for numbers like "10."
            
            // Statement 1
            const statement1 = `${statementNumber}. This statement made by me accurately sets out the evidence that I would be prepared, if necessary, to give in court as a witness. The statement is true to the best of my knowledge and belief and I make it knowing that, if it is tendered in evidence, I will be liable to prosecution if I have wilfully stated in it anything that I know to be false, or do not believe to be true.`;
            const lines1 = doc.splitTextToSize(statement1.substring(3), maxWidth - numberIndent);
            
            // Aggressive page filling - use maximum available space
            const spaceNeeded = lines1.length * 7 + 7;
            const spaceAvailable = 250 - yPosition; // Increased available space even more
            
            if (spaceNeeded > spaceAvailable && spaceAvailable > 7) { // Much lower minimum space requirement
                const linesThatFit = Math.floor((spaceAvailable - 7) / 7); // Reserve minimal space for gap
                const firstPageLines = lines1.slice(0, linesThatFit);
                const remainingLines = lines1.slice(linesThatFit);
                
                doc.text(`${statementNumber}.`, margin, yPosition);
                doc.text(firstPageLines, margin + numberIndent, yPosition);
                
                addSignatures(yPosition + firstPageLines.length * 7);
                doc.addPage();
                addPageHeader(2);
                yPosition = 30;
                
                doc.text(remainingLines, margin + numberIndent, yPosition);
                yPosition += remainingLines.length * 7 + 7; // Fixed spacing regardless of content length
            } else if (spaceNeeded > spaceAvailable) {
                addSignatures(yPosition);
                doc.addPage();
                addPageHeader(2);
                yPosition = 30;
                doc.text(`${statementNumber}.`, margin, yPosition);
                doc.text(lines1, margin + numberIndent, yPosition);
                yPosition += lines1.length * 7 + 7; // Fixed spacing regardless of content length
            } else {
                doc.text(`${statementNumber}.`, margin, yPosition);
                doc.text(lines1, margin + numberIndent, yPosition);
                yPosition += lines1.length * 7 + 7; // Fixed spacing regardless of content length
            }
            statementNumber++;

            // Statement 2 - Age
            const statement2Text = `I am ${age} years of age.`;
            if (yPosition + 14 > 240) { // Increased available space
                addSignatures(yPosition);
                doc.addPage();
                addPageHeader(doc.internal.getNumberOfPages());
                yPosition = 30;
            }
            doc.text(`${statementNumber}.`, margin, yPosition);
            doc.text(statement2Text, margin + numberIndent, yPosition);
            yPosition += 12; // Normal spacing for other paragraphs
            statementNumber++;

            // Statement 3 - AI Declaration
            const statement3Text = `I have not used Generative Artificial Intelligence (Gen AI) in the creation of this statement or any attached exhibits.`;
            const lines3 = doc.splitTextToSize(statement3Text, maxWidth - numberIndent);
            
            // Aggressive page filling for statement 3
            const space3Needed = lines3.length * 7 + 7;
            const space3Available = 240 - yPosition; // Increased available space
            
            if (space3Needed > space3Available && space3Available > 10) {
                const lines3ThatFit = Math.floor((space3Available - 7) / 7);
                const firstPage3Lines = lines3.slice(0, lines3ThatFit);
                const remaining3Lines = lines3.slice(lines3ThatFit);
                
                doc.text(`${statementNumber}.`, margin, yPosition);
                doc.text(firstPage3Lines, margin + numberIndent, yPosition);
                
                addSignatures(yPosition + firstPage3Lines.length * 7);
                doc.addPage();
                addPageHeader(doc.internal.getNumberOfPages());
                yPosition = 30;
                
                doc.text(remaining3Lines, margin + numberIndent, yPosition);
                yPosition += remaining3Lines.length * 7 + 7; // Fixed spacing regardless of content length
            } else if (space3Needed > space3Available) {
                addSignatures(yPosition);
                doc.addPage();
                addPageHeader(doc.internal.getNumberOfPages());
                yPosition = 30;
                doc.text(`${statementNumber}.`, margin, yPosition);
                doc.text(lines3, margin + numberIndent, yPosition);
                yPosition += lines3.length * 7 + 7; // Fixed spacing regardless of content length
            } else {
                doc.text(`${statementNumber}.`, margin, yPosition);
                doc.text(lines3, margin + numberIndent, yPosition);
                yPosition += lines3.length * 7 + 7;
            }
            statementNumber++;

            // Process user statement text with aggressive page filling
            const paragraphs = statementText.split(/\n\s*\n/).filter(p => p.trim() !== '');
            
            paragraphs.forEach(paragraph => {
                const paragraphText = paragraph.trim();
                const lines = doc.splitTextToSize(paragraphText, maxWidth - numberIndent);
                
                // Aggressive page filling for user paragraphs
                const spaceNeeded = lines.length * 7 + 7;
                const spaceAvailable = 240 - yPosition; // Increased available space
                
                if (spaceNeeded > spaceAvailable && spaceAvailable > 10) {
                    const linesThatFit = Math.floor((spaceAvailable - 7) / 7);
                    const firstPageLines = lines.slice(0, linesThatFit);
                    const remainingLines = lines.slice(linesThatFit);
                    
                    doc.text(`${statementNumber}.`, margin, yPosition);
                    doc.text(firstPageLines, margin + numberIndent, yPosition);
                    
                    addSignatures(yPosition + firstPageLines.length * 7);
                    doc.addPage();
                    addPageHeader(doc.internal.getNumberOfPages());
                    yPosition = 30;
                    
                    doc.text(remainingLines, margin + numberIndent, yPosition);
                    yPosition += remainingLines.length * 7 + 7; // Fixed spacing regardless of content length
                } else if (spaceNeeded > spaceAvailable) {
                    addSignatures(yPosition);
                    doc.addPage();
                    addPageHeader(doc.internal.getNumberOfPages());
                    yPosition = 30;
                    doc.text(`${statementNumber}.`, margin, yPosition);
                    doc.text(lines, margin + numberIndent, yPosition);
                    yPosition += lines.length * 7 + 7; // Fixed spacing regardless of content length
                } else {
                    doc.text(`${statementNumber}.`, margin, yPosition);
                    doc.text(lines, margin + numberIndent, yPosition);
                    yPosition += lines.length * 7 + 7;
                }
                statementNumber++;
            });

            // Add signatures to the last page
            addSignatures(yPosition);

            // Add page numbers and headers to all pages
            const pageCount = doc.internal.getNumberOfPages();
            for (let i = 1; i <= pageCount; i++) {
                doc.setPage(i);
                addPageHeader(i);
                doc.setFontSize(10);
                doc.setFont(undefined, 'normal');
                doc.text(`Page ${i} of ${pageCount}`, pageWidth - 40, 285);
            }

            // Save the PDF
            const fileName = `Statement of ${name.replace(/\s+/g, '_')}.pdf`;
            doc.save(fileName);
        }

        // Helper function to check if canvas is blank
        function isCanvasBlank(canvas) {
            const context = canvas.getContext('2d');
            const pixelBuffer = new Uint32Array(
                context.getImageData(0, 0, canvas.width, canvas.height).data.buffer
            );
            return !pixelBuffer.some(color => color !== 0);
        }
    </script>
</body>
</html>
