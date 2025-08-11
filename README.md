# SlopeVal
​A simple, offline slope calculator for web browsers. It solves for slope (%), fall, or distance when two values are provided. It handles multiple units for distance (inches, feet, yards) and fall (inches, feet), making it a versatile tool for quick calculations.
Slope (%)= \\frac{Fall}{Distance} \\times 100
To solve for any of the three variables, we can rearrange this formula:
 * Solving for Slope (%): Slope = (\\frac{Fall}{Distance}) \\times 100
 * Solving for Fall: Fall = \\frac{Slope}{100} \\times Distance
 * Solving for Distance: Distance = \\frac{Fall}{\\frac{Slope}{100}} or Distance = \\frac{Fall \\times 100}{Slope}
The key is that all units must be consistent before the calculation. I will use inches as the base unit for all calculations.
 * Yards to Inches: yards \\times 36
 * Feet to Inches: feet \\times 12
 * Fall (Feet to Inches): feet \\times 12
 * Fall (Inches): just inches
The HTML, CSS, and JavaScript Code
Below is the complete code for a simple, functional slope calculator. You can copy and paste this into a text file and save it with a .html extension (e.g., slope_calculator.html). Then, you can open this file in your web browser.
slope_calculator.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Slope Calculator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            padding: 20px;
            background-color: #f4f4f4;
            color: #333;
            max-width: 600px;
            margin: auto;
            line-height: 1.6;
        }
        h1 {
            color: #0056b3;
            text-align: center;
        }
        .container {
            background: #fff;
            padding: 25px;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }
        .input-group {
            margin-bottom: 20px;
        }
        .input-group label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        .input-row {
            display: flex;
            gap: 10px;
            align-items: center;
        }
        .input-row input[type="number"] {
            flex-grow: 1;
            padding: 8px;
            border: 1px solid #ccc;
            border-radius: 4px;
            font-size: 1em;
            width: 100%;
            box-sizing: border-box;
        }
        .input-row select {
            padding: 8px;
            border: 1px solid #ccc;
            border-radius: 4px;
            font-size: 1em;
        }
        button {
            width: 100%;
            padding: 10px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 1.1em;
            margin-top: 10px;
        }
        button:hover {
            background-color: #0056b3;
        }
        .result {
            margin-top: 25px;
            padding: 15px;
            background-color: #e9ecef;
            border-radius: 4px;
            font-size: 1.2em;
            text-align: center;
        }
        .error {
            color: red;
            margin-top: 10px;
            text-align: center;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>Slope Calculator</h1>
    <p>Enter any two of the three values to calculate the missing one.</p>

    <div class="input-group">
        <label for="distance-input">Distance</label>
        <div class="input-row">
            <input type="number" id="distance-input" placeholder="Enter distance value">
            <select id="distance-unit">
                <option value="inches">Inches</option>
                <option value="feet">Feet</option>
                <option value="yards">Yards</option>
            </select>
        </div>
    </div>

    <div class="input-group">
        <label for="fall-input">Fall</label>
        <div class="input-row">
            <input type="number" id="fall-input" placeholder="Enter fall value">
            <select id="fall-unit">
                <option value="inches">Inches</option>
                <option value="feet">Feet</option>
            </select>
        </div>
    </div>

    <div class="input-group">
        <label for="slope-input">Slope (%)</label>
        <input type="number" id="slope-input" placeholder="Enter slope percentage">
    </div>

    <button onclick="calculateSlope()">Calculate</button>

    <div id="result" class="result" style="display:none;"></div>
    <div id="error-message" class="error" style="display:none;"></div>
</div>

<script>
function calculateSlope() {
    // Get all input values and their units
    const distanceVal = parseFloat(document.getElementById('distance-input').value);
    const distanceUnit = document.getElementById('distance-unit').value;
    const fallVal = parseFloat(document.getElementById('fall-input').value);
    const fallUnit = document.getElementById('fall-unit').value;
    const slopeVal = parseFloat(document.getElementById('slope-input').value);

    // Get result and error elements
    const resultElement = document.getElementById('result');
    const errorElement = document.getElementById('error-message');

    // Hide previous results/errors
    resultElement.style.display = 'none';
    errorElement.style.display = 'none';

    // Count how many values are provided
    let providedValues = 0;
    if (!isNaN(distanceVal)) providedValues++;
    if (!isNaN(fallVal)) providedValues++;
    if (!isNaN(slopeVal)) providedValues++;

    // Validation checks
    if (providedValues < 2 || providedValues > 2) {
        errorElement.textContent = 'Please provide exactly two of the three values.';
        errorElement.style.display = 'block';
        return;
    }
    
    if ((!isNaN(distanceVal) && distanceVal < 0) || (!isNaN(fallVal) && fallVal < 0) || (!isNaN(slopeVal) && slopeVal < 0)) {
         errorElement.textContent = 'Values cannot be negative.';
         errorElement.style.display = 'block';
         return;
    }

    // Convert all inputs to a common unit (inches)
    let distanceInches, fallInches;

    if (!isNaN(distanceVal)) {
        if (distanceUnit === 'feet') {
            distanceInches = distanceVal * 12;
        } else if (distanceUnit === 'yards') {
            distanceInches = distanceVal * 36;
        } else { // 'inches'
            distanceInches = distanceVal;
        }
    }

    if (!isNaN(fallVal)) {
        if (fallUnit === 'feet') {
            fallInches = fallVal * 12;
        } else { // 'inches'
            fallInches = fallVal;
        }
    }

    let calculatedValue, resultText = '';

    // Calculate the missing value
    if (isNaN(slopeVal)) { // Calculate Slope
        if (distanceInches === 0) {
            errorElement.textContent = 'Distance cannot be zero when calculating slope.';
            errorElement.style.display = 'block';
            return;
        }
        calculatedValue = (fallInches / distanceInches) * 100;
        resultText = `Calculated Slope: ${calculatedValue.toFixed(2)} %`;
    } else if (isNaN(fallVal)) { // Calculate Fall
        if (slopeVal < 0) {
            errorElement.textContent = 'Slope percentage cannot be negative.';
            errorElement.style.display = 'block';
            return;
        }
        calculatedValue = (slopeVal / 100) * distanceInches;
        
        // Convert back to original Fall unit for display
        if (fallUnit === 'feet') {
            calculatedValue /= 12;
            resultText = `Calculated Fall: ${calculatedValue.toFixed(2)} ${fallUnit}`;
        } else { // 'inches'
            resultText = `Calculated Fall: ${calculatedValue.toFixed(2)} ${fallUnit}`;
        }

    } else if (isNaN(distanceVal)) { // Calculate Distance
        if (slopeVal === 0) {
            errorElement.textContent = 'Slope cannot be zero when calculating distance.';
            errorElement.style.display = 'block';
            return;
        }
        calculatedValue = (fallInches / (slopeVal / 100));
        
        // Convert back to original Distance unit for display
        if (distanceUnit === 'yards') {
            calculatedValue /= 36;
            resultText = `Calculated Distance: ${calculatedValue.toFixed(2)} ${distanceUnit}`;
        } else if (distanceUnit === 'feet') {
            calculatedValue /= 12;
            resultText = `Calculated Distance: ${calculatedValue.toFixed(2)} ${distanceUnit}`;
        } else { // 'inches'
            resultText = `Calculated Distance: ${calculatedValue.toFixed(2)} ${distanceUnit}`;
        }
    }
    
    // Display the result
    if (resultText) {
        resultElement.textContent = resultText;
        resultElement.style.display = 'block';
    }
}
</script>

</body>
</html>
