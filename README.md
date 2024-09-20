<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Grade Calculator</title>
    <style>
        body {
            font-family: 'Verdana', sans-serif;
            margin: auto;
            display: flex;
            background-color: #E5989B;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        .container {
            max-width: 1200px;
            margin: 100px auto;
            padding: 20px;
            background-color: white;
            border-radius: 10px;
            width: 400px;
            text-align: center;
            box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1); /* Adds shadow for professional look */
        }
        h1 {
            text-align: center;
            color: #333;
            margin-bottom: 1.5rem;
            font-size: 2.2rem;
            font-weight: 700;
            text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.1);
        }
        label {
            font-family: 'Arial', sans-serif;
            display: block;
            margin-bottom: 8px;
            font-size: 1.1rem;
            font-weight: 600;
            color: #555;
        }
        input {
            width: 50%;
            padding: 8px;
            margin-bottom: 12px;
            border: 1px solid #ddd;
            border-radius: 8px;
            font-size: 1rem;
            box-sizing: border-box;
            outline: none;
            transition: border-color 0.3s ease;
        }
        input:focus {
            border-color: #C3B1E1;
        }
        /* Button container styling */
        .button-container {
            margin-top: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        button {
            padding: 12px 24px;
            background-color: #C3B1E1;
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 1.1rem;
            font-weight: bold;
            transition: background-color 0.3s ease, transform 0.3s ease;
        }
        button:hover {
            background-color: #E1CCB7;
            transform: scale(1.05);
        }
        .result-container {
            width: 100%;
            margin-top: 20px;
        }
        .result {
            font-size: 1.15rem;
        }
        .result ul {
            list-style-type: none; /* Removes bullets */
            padding: 0;
            margin: 0;
            text-align: left;
        }
        .error {
            color: red;
            display: none;
            font-weight: 600;
        }
        .success {
            color: #2ecc71;
            font-weight: 600;
        }
        .congratulations {
            color: #f39c12;
            font-weight: 700;
            font-size: 1.2rem;
            margin-top: 1rem;
        }
        .chance {
            color: #3498db;
            font-weight: 600;
            margin-top: 1rem;
        }
        p {
            text-align: center;
            display: none;
        }
    </style>
    <!-- Include PyScript -->
    <link rel="stylesheet" href="https://pyscript.net/latest/pyscript.css" />
    <script defer src="https://pyscript.net/latest/pyscript.js"></script>
</head>
<body>
    <div class="container">
        <h1>Grade Calculator</h1>
        <form id="gradeForm" onsubmit="return false;">
            <label for="prelim">Enter the Prelim Grade:</label>
            <input type="number" id="prelim" name="prelim" step="0.01" min="0" max="100" required onchange="handleChange(this);">
            
            <!-- Button Container -->
            <div class="button-container">
                <button type="button" id="calculateBtn">Calculate</button>
            </div>

            <!-- New Result Container at the bottom -->
            <div class="result-container">
                <div id="result" class="result" style="display: none;">
                    <ul>
                        <li><strong>Prelim Grade:</strong> <span id="prelimGrade"></span></li>
                        <li><strong>Required Midterm Grade:</strong> <span id="midtermGrade"></span></li>
                        <li><strong>Required Final Grade:</strong> <span id="finalGrade"></span></li>
                        <li id="passMessage"></li>
                        <li id="deansMessage"></li>
                    </ul>
                </div>
            </div>
        </form>
    </div>

    <py-script>
        from pyscript import Element

        def calculate_grade(event):
            try:
                prelim = float(Element("prelim").element.value)
            except ValueError:
                Element("result").element.style.display = "block"
                Element("result").write("Please enter a valid prelim grade.")
                return

            passing_grade = 75
            deans_lister_grade = 90
            prelim_percent = 0.20
            midterm_percent = 0.30
            final_percent = 0.50

            # Check if prelim grade is valid
            if prelim < 0 or prelim > 100:
                Element("result").element.style.display = "block"
                Element("result").write("Please enter a valid prelim grade between 0 and 100.")
                return

            # Calculate the required midterm and final grades to pass
            current_total = prelim * prelim_percent
            required_total = passing_grade - current_total

            if required_total > 0:
                required_midterm_and_final = required_total / (midterm_percent + final_percent)
                
                # Determine the pass message
                pass_message = (
                    "It is difficult to pass, as the required grades are very high."
                    if required_midterm_and_final > 90
                    else "You have a chance to pass!"
                )
            else:
                required_midterm_and_final = 0
                pass_message = "Your current grade is high enough to pass!"

            # Calculate the required midterm and final grades for Dean's Lister
            if prelim >= deans_lister_grade:
                deans_message = "You already qualify for Dean's Lister based on your Prelim grade!"
            else:
                required_deans_total = deans_lister_grade - current_total
                required_deans_midfinal = required_deans_total / (midterm_percent + final_percent)
                if required_deans_midfinal > 100:
                    deans_message = "The required grade is above 100%."
                else:
                    deans_message = (
                        f"The required grade for you to be a Dean’s Lister is "
                        f"{required_deans_midfinal:.2f}% (midterm) and {required_deans_midfinal:.2f}% (finals)."
                    )

            # Update the content of the result elements
            Element("prelimGrade").element.innerHTML = f"{prelim:.2f}%"
            Element("midtermGrade").element.innerHTML = f"{required_midterm_and_final:.2f}%"
            Element("finalGrade").element.innerHTML = f"{required_midterm_and_final:.2f}%"
            Element("passMessage").element.innerHTML = pass_message
            Element("deansMessage").element.innerHTML = deans_message

            # Show the result section
            Element("result").element.style.display = "block"

        def handle_keypress(event):
            if event.key == "Enter":
                event.preventDefault()  # Prevent form submission
                calculate_grade(event)
        
        # Attach the event listener to the button
        Element("calculateBtn").element.onclick = calculate_grade

        # Attach the event listener to the input field
        Element("prelim").element.onkeydown = handle_keypress
    </py-script>
    <script>
        function handleChange(input) {
            if (input.value < 0) input.value = 0;
            if (input.value > 100) input.value = 100;
        }
    </script>
</body>
</html>
