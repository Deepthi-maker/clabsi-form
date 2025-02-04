<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CLABSI Quality Check Form</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
            padding: 20px;
            background-color: #f9f9f9;
        }
        .form-section {
            background-color: white;
            padding: 15px;
            margin-bottom: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        }
        label {
            display: block;
            margin-top: 10px;
        }
        button {
            padding: 10px 20px;
            margin-top: 20px;
            cursor: pointer;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <h1>CLABSI Quality Check Form</h1>
    <div class="form-section">
        <label>Date: <input type="date" id="date"></label>
        <label>Room #: <input type="text" id="room"></label>
        <label>Unit:
            <select id="unit">
                <option value="TICU">TICU</option>
                <option value="MICU">MICU</option>
                <option value="5A/C">5A/C</option>
                <option value="6A (CMCU)">6A (CMCU)</option>
                <option value="6C">6C</option>
                <option value="7A">7A</option>
                <option value="7C/NICU">7C/NICU</option>
                <option value="8A">8A</option>
                <option value="8C">8C</option>
                <option value="9A">9A</option>
                <option value="9C">9C</option>
                <option value="10A/C">10A/C</option>
                <option value="11A">11A</option>
                <option value="11C/CICU">11C/CICU</option>
                <option value="12A">12A</option>
                <option value="12C/CT-SICU">12C/CT-SICU</option>
            </select>
        </label>

        <label>Line Type:
            <select id="lineType" onchange="showQuestions()">
                <option value="">Select Line</option>
                <option value="PICC">PICC</option>
                <option value="CVC">CVC</option>
                <option value="VASCATH">VASCATH</option>
                <option value="PORT">PORT</option>
                <option value="Arterial Line">Arterial Line</option>
                <option value="PIV">Peripheral IV (PIV)</option>
            </select>
        </label>

        <div id="questions"></div>

        <button onclick="submitForm()">Submit</button>
    </div>

    <script>
        function showQuestions() {
            const lineType = document.getElementById('lineType').value;
            const questionsDiv = document.getElementById('questions');
            questionsDiv.innerHTML = '';

            if (lineType) {
                const questions = [
                    'Line Necessity Verified',
                    'Dressing is Clean, Dry, and Secure',
                    'Dressing is Within Valid Date Range',
                    'CHG Disk is Correctly Placed',
                    'Primary IV Tubing (Continuous) is Within Date',
                    'Secondary IV Tubing (Intermittent) is Within Date',
                    'Two Medication Labels are in Place',
                    'Disinfecting Caps are on All Unused Hubs',
                    'All Hubs are Correctly Connected or Disinfecting Tip is in Place'
                ];

                questions.forEach(q => {
                    questionsDiv.innerHTML += `
                        <label>${q}:
                            <input type="radio" name="${q}" value="Yes"> Yes
                            <input type="radio" name="${q}" value="No"> No
                            <input type="radio" name="${q}" value="N/A"> N/A
                        </label>
                    `;
                });

                questionsDiv.innerHTML += `
                    <label>Notes/Actions Taken:
                        <textarea id="notes" rows="3" cols="30"></textarea>
                    </label>
                `;
            }
        }

        function submitForm() {
            const date = document.getElementById('date').value;
            const room = document.getElementById('room').value;
            const unit = document.getElementById('unit').value;
            const lineType = document.getElementById('lineType').value;
            const notes = document.getElementById('notes') ? document.getElementById('notes').value : '';

            const responses = {};
            document.querySelectorAll('#questions input[type="radio"]:checked').forEach(input => {
                responses[input.name] = input.value;
            });

            const formData = {
                date: date,
                room: room,
                unit: unit,
                lineType: lineType,
                responses: JSON.stringify(responses),
                notes: notes
            };

            fetch('https://script.google.com/a/macros/mail.rmu.edu/s/AKfycbzznkrbZZZtaKWA0bCUuLqrPg2BfHzZXTxi9IrlVTqIxhfrGd-nQMIafLEwVzLwDoRcxg/exec', {
                method: 'POST',
                body: JSON.stringify(formData),
                headers: {
                    'Content-Type': 'application/json'
                }
            })
            .then(response => response.json())
            .then(data => {
                if (data.result === 'Success') {
                    alert('Form submitted successfully!');
                } else {
                    alert('Error: ' + data.message);
                }
            })
            .catch(error => {
                alert('Error: ' + error.message);
            });
        }
    </script>
</body>
</html>
