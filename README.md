<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>What Broke? 🐞</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&display=swap');
        :root {
            --bg-color: #1a1a2e;
            --primary-color: #16213e;
            --accent-color: #e94560;
            --secondary-accent: #0f3460;
            --text-color: #f0f0f0;
            --success-color: #38b2ac;
            --error-color: #f56565;
            --shadow-color: rgba(0, 0, 0, 0.4);
            --border-radius: 12px;
        }
        body {
            font-family: 'Inter', sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            margin: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            padding: 20px;
            box-sizing: border-box;
        }
        .container {
            width: 100%;
            max-width: 900px;
            background: var(--primary-color);
            padding: 25px;
            border-radius: var(--border-radius);
            box-shadow: 0 10px 30px var(--shadow-color);
            transition: transform 0.3s ease;
        }
        h1, h2 {
            text-align: center;
            color: var(--accent-color);
            font-weight: 800;
            margin-top: 0;
        }
        h1 { font-size: 2.5rem; }
        h2 { font-size: 1.8rem; }

        .button-group {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin-top: 20px;
        }
        button, .mcq-option label {
            padding: 12px 24px;
            font-size: 1rem;
            font-weight: 600;
            border: none;
            border-radius: var(--border-radius);
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 4px 10px var(--shadow-color);
        }
        button {
            background: var(--accent-color);
            color: var(--text-color);
        }
        button:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 15px var(--shadow-color);
            background: #ff6b8a;
        }
        button.secondary {
            background: var(--secondary-accent);
        }
        button.secondary:hover {
            background: #2a588b;
        }
        .selection-screen {
            text-align: center;
        }
        .game-area {
            display: none;
            flex-direction: column;
            gap: 20px;
        }
        #quiz-card {
            background: var(--secondary-accent);
            padding: 20px;
            border-radius: var(--border-radius);
            box-shadow: inset 0 2px 5px rgba(0,0,0,0.2);
        }
        #code-display, #fix-code-editor {
            background: var(--bg-color);
            padding: 15px;
            border-radius: var(--border-radius);
            font-family: 'Courier New', monospace;
            white-space: pre-wrap;
            word-wrap: break-word;
            font-size: 0.9rem;
            line-height: 1.5;
            color: #d1d1e9;
            border: 1px solid #333;
        }
        #fix-code-editor {
            width: calc(100% - 30px);
            height: 200px;
            resize: vertical;
            border: none;
            outline: none;
        }
        .mcq-options {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }
        .mcq-option label {
            display: block;
            background: var(--bg-color);
            text-align: left;
            transition: all 0.2s ease;
        }
        .mcq-option label:hover {
            background: var(--secondary-accent);
            color: var(--accent-color);
        }
        input[type="radio"] { display: none; }
        input[type="radio"]:checked + label {
            background: var(--accent-color);
            color: var(--text-color);
            box-shadow: 0 2px 5px var(--shadow-color);
        }

        #feedback-area, #hint-modal {
            padding: 15px;
            border-radius: var(--border-radius);
            text-align: center;
            display: none;
        }
        #feedback-area.correct { background: var(--success-color); }
        #feedback-area.incorrect { background: var(--error-color); }
        .modal {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: var(--secondary-accent);
            padding: 30px;
            z-index: 1000;
            box-shadow: 0 10px 30px var(--shadow-color);
            display: none;
            flex-direction: column;
            gap: 20px;
            max-width: 80%;
            text-align: left;
            border-radius: var(--border-radius);
        }
        .modal h3 {
            margin-top: 0;
            color: var(--accent-color);
        }
        .modal-content {
            white-space: pre-wrap;
            word-wrap: break-word;
        }
        #score-board, #timer-display {
            background: var(--secondary-accent);
            padding: 10px 15px;
            border-radius: var(--border-radius);
            text-align: center;
        }
        #score-board span, #timer-display span {
            font-size: 1.2rem;
            font-weight: 800;
            color: var(--accent-color);
        }
        .stats-and-info {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }
        #share-link-box {
            display: flex;
            gap: 10px;
            margin-top: 10px;
        }
        #share-link-input {
            flex-grow: 1;
            padding: 8px;
            border-radius: var(--border-radius);
            border: 1px solid var(--bg-color);
            background: var(--bg-color);
            color: var(--text-color);
        }
        @media (max-width: 600px) {
            .container { padding: 15px; }
            h1 { font-size: 1.8rem; }
            h2 { font-size: 1.4rem; }
            button { width: 100%; }
            .button-group { flex-direction: column; }
            .stats-and-info { flex-direction: column; gap: 10px; }
            #score-board, #timer-display { width: 100%; }
        }
    </style>
</head>
<body>

<div class="container">
    <div id="auth-screen" class="selection-screen">
        <h1>Welcome, Debugger! 🐞</h1>
        <p>Enter your name to start your journey:</p>
        <div class="flex flex-col gap-4 items-center">
            <input type="text" id="player-name-input" placeholder="Your Name" class="px-4 py-2 rounded-lg text-black w-2/3">
            <button onclick="setNameAndProceed()">Start</button>
        </div>
    </div>
    
    <div id="start-screen" class="selection-screen" style="display: none;">
        <h2>Choose your debugging level! 🐞</h2>
        <div class="button-group">
            <button onclick="selectLevel('beginner')">🟢 Beginner</button>
            <button onclick="selectLevel('intermediate')">🟡 Intermediate</button>
            <button onclick="selectLevel('advanced')">🔴 Advanced</button>
        </div>
    </div>

    <div id="mode-screen" class="selection-screen" style="display: none;">
        <h2>Choose your game mode! 🎮</h2>
        <div class="button-group">
            <button onclick="startGame('time-attack')">⚡ Time Attack (60s)</button>
            <button onclick="startGame('fix-the-bug')">✍ Fix the Bug</button>
            <button onclick="startGame('multiple-choice')">🧐 Multiple Choice</button>
        </div>
    </div>

    <div id="game-screen" class="game-area">
        <div class="stats-and-info">
            <div id="score-board">Score: <span>0</span></div>
            <div id="timer-display">Time: <span>60</span>s</div>
        </div>
        <div id="quiz-card">
            <h2 id="question-title"></h2>
            <p id="question-text"></p>
            <pre id="code-display"></pre>
            <textarea id="fix-code-editor" style="display: none;"></textarea>

            <div id="mcq-options" class="mcq-options" style="display: none;"></div>
        </div>

        <div class="button-group">
            <button id="submit-btn" onclick="checkAnswer()">Submit</button>
            <button id="hint-btn" class="secondary" onclick="showHint()">💡 Hint</button>
        </div>
    </div>

    <div id="feedback-area" style="display: none;">
        <h2 id="feedback-title"></h2>
        <p id="explanation"></p>
        <div id="diff-view" style="display: none;">
            <h3>Broken Code vs. Fixed Code</h3>
            <pre id="broken-code-diff"></pre>
            <pre id="fixed-code-diff"></pre>
        </div>
        <div class="button-group">
            <button id="next-btn" onclick="nextQuestion()">➡ Next Bug</button>
            <button onclick="endGame()">🏁 End Game</button>
        </div>
    </div>

    <div id="end-screen" class="game-area">
        <h2>Game Over! 🏁</h2>
        <p id="final-score"></p>
        <div id="performance-summary" style="margin-top: 20px; text-align: left;">
            <h3>Your Rewards History 🏆</h3>
            <div id="rewards-history" class="overflow-y-auto max-h-48 bg-gray-800 p-4 rounded-lg">
                <p>No rewards found. Play a game to earn one! 🐞</p>
            </div>
            <h3 class="mt-4">Rewards & Tips 💡</h3>
            <p id="reward-message"></p>
            <p id="coding-tip"></p>
        </div>
        <div class="button-group">
            <button onclick="resetGame()">🔄 Play Again</button>
            <button onclick="showShareModal()">🔗 Share</button>
        </div>
    </div>

        <div id="hint-modal" class="modal">
        <h3>💡 Hint</h3>
        <p id="hint-modal-text" class="modal-content"></p>
        <button onclick="closeHintModal()">Close</button>
    </div>

        <div id="message-modal" class="modal">
        <h3 id="message-modal-title"></h3>
        <p id="message-modal-text" class="modal-content"></p>
        <button onclick="closeMessageModal()">OK</button>
    </div>
    
        <div id="share-modal" class="modal">
        <h3>Share This Quiz! 🔗</h3>
        <p>Copy the link below and share it with your friends to challenge them!</p>
        <div id="share-link-box">
            <input type="text" id="share-link-input" readonly>
            <button onclick="copyToClipboard()">Copy</button>
        </div>
        <button onclick="closeShareModal()">Close</button>
    </div>
</div>

<script type="module">
    // --- Quiz Data ---
    const quizData = [
        // --- Beginner Multiple Choice Questions ---
        {
            language: "JavaScript",
            errorType: "Syntax Error",
            mode: "multiple-choice",
            level: "beginner",
            question: "This function has a syntax error. What's wrong? 🐞",
            brokenCode: "function greet(name) {\n  console.log('Hello, ' + name;\n}",
            options: [
                "Missing closing parenthesis on the console.log call.",
                "Missing semicolon at the end of the line.",
                "The 'name' parameter is not defined.",
                "Function keyword is misspelled."
            ],
            correctAnswer: "Missing closing parenthesis on the console.log call.",
            explanation: "The console.log statement is missing its closing parenthesis. All function calls in JavaScript must have parentheses, even if they don't take arguments."
        },
        {
            language: "Python",
            errorType: "TypeError",
            mode: "multiple-choice",
            level: "beginner",
            question: "What is the problem with this code that attempts to concatenate a string and an integer?",
            brokenCode: "age = 25\nprint(\"I am \" + age + \" years old.\")",
            options: [
                "Python does not allow string concatenation.",
                "The age variable needs to be a string.",
                "The print function requires a semicolon.",
                "The code is fine and will not produce an error."
            ],
            correctAnswer: "The age variable needs to be a string.",
            explanation: "You cannot concatenate a string and an integer directly in Python. You must first convert the integer to a string using str(age) or use an f-string."
        },
        {
            language: "HTML",
            errorType: "Logical Error",
            mode: "multiple-choice",
            level: "beginner",
            question: "Why does the image not show up? 🐞",
            brokenCode: "<img src=\"images/picture.jpg\" alt=\"A beautiful image\">\n",
            options: [
                "The src attribute is missing.",
                "The file path is incorrect or the image file is missing.",
                "The img tag is not a self-closing tag.",
                "The alt attribute is not a valid attribute."
            ],
            correctAnswer: "The file path is incorrect or the image file is missing.",
            explanation: "The HTML code is syntactically correct. A likely reason for the image not appearing is that the file path in the src attribute is wrong, or the picture.jpg file does not exist at that location."
        },
        {
            language: "CSS",
            errorType: "Syntax Error",
            mode: "multiple-choice",
            level: "beginner",
            question: "Why is the text color not changing to blue? 🐞",
            brokenCode: "p { color: blue }",
            options: [
                "Missing a closing curly brace }.",
                "Missing a semicolon ; after the property value.",
                "The selector p is incorrect.",
                "The color name 'blue' is not valid."
            ],
            correctAnswer: "Missing a semicolon ; after the property value.",
            explanation: "In CSS, each property declaration within a rule must be terminated with a semicolon. The correct syntax is p { color: blue; }."
        },
        {
            language: "JavaScript",
            errorType: "ReferenceError",
            mode: "multiple-choice",
            level: "beginner",
            question: "The following code produces a ReferenceError. Why? 🐞",
            brokenCode: "let a = 10;\nconst b = 20;\n\nfunction myFunc() {\n  var c = 30;\n}\n\nconsole.log(c);",
            options: [
                "The variable c is block-scoped.",
                "The var keyword is deprecated.",
                "The variable c is function-scoped.",
                "The variable a is not declared with const."
            ],
            correctAnswer: "The variable c is function-scoped.",
            explanation: "Variables declared with var are function-scoped. They are only accessible within the function where they are declared, not outside of it."
        },
        
        // --- Beginner Fix the Bug Questions ---
        {
            language: "Python",
            errorType: "Logic Error",
            mode: "fix-the-bug",
            level: "beginner",
            question: "This function is supposed to return the sum of all numbers in a list, but it's returning the sum of just the last number. Fix it! 🐞",
            brokenCode: "def sum_list(numbers):\n    total = 0\n    for num in numbers:\n        total = num\n    return total",
            correctCode: "def sum_list(numbers):\n    total = 0\n    for num in numbers:\n        total += num\n    return total",
            explanation: "The line total = num reassigns the total to the current number in the loop, instead of adding to it. The correct operator for addition and assignment is +=."
        },
        {
            language: "C++",
            errorType: "Runtime Error",
            mode: "fix-the-bug",
            level: "beginner",
            question: "This C++ program is trying to access an element outside the bounds of the array. Fix the loop condition. 🐞",
            brokenCode: "#include <iostream>\nint main() {\n    int arr[5] = {1, 2, 3, 4, 5};\n    for (int i = 0; i <= 5; ++i) {\n        std::cout << arr[i] << \" \";\n    }\n    return 0;\n}",
            correctCode: "#include <iostream>\nint main() {\n    int arr[5] = {1, 2, 3, 4, 5};\n    for (int i = 0; i < 5; ++i) {\n        std::cout << arr[i] << \" \";\n    }\n    return 0;\n}",
            explanation: "Arrays in C++ are 0-indexed, meaning a 5-element array has indices from 0 to 4. The condition i <= 5 will try to access arr[5], which is out of bounds. The correct condition should be i < 5."
        },
        {
            language: "JavaScript",
            errorType: "Logical Error",
            mode: "fix-the-bug",
            level: "beginner",
            question: "This code is supposed to check if a number is even, but it's not working correctly. Find the bug! 🐞",
            brokenCode: "let number = 10;\nif (number / 2 == 0) {\n  console.log('Even');\n} else {\n  console.log('Odd');\n}",
            correctCode: "let number = 10;\nif (number % 2 == 0) {\n  console.log('Even');\n} else {\n  console.log('Odd');\n}",
            explanation: "To check if a number is even, you need to find the remainder when it's divided by 2. This is done with the modulo operator (%). The division operator (/) simply divides the number and won't give a remainder."
        },

        // --- Intermediate Multiple Choice Questions ---
        {
            language: "Python",
            errorType: "Syntax Error",
            mode: "multiple-choice",
            level: "intermediate",
            question: "This Python code has an indentation error. How should it be fixed? 🐞",
            brokenCode: "def my_function():\nprint(\"Hello\")",
            options: [
                "Add an indent to the print statement.",
                "Remove the parenthesis from the print function.",
                "Add a semicolon at the end of the line.",
                "Remove the colon after the function definition."
            ],
            correctAnswer: "Add an indent to the print statement.",
            explanation: "Python uses indentation to define blocks of code. The print statement inside the my_function must be indented to be considered part of the function body."
        },
        {
            language: "JavaScript",
            errorType: "Asynchronous Bug",
            mode: "multiple-choice",
            level: "intermediate",
            question: "This code returns 'undefined' immediately. The fetchData function needs to be corrected to handle the asynchronous call.",
            brokenCode: "async function fetchData() {\n  const response = fetch('https://api.example.com/data');\n  const data = response.json();\n  return data;\n}\n\nfetchData().then(result => console.log(result));",
            options: [
                "The fetch function is misspelled.",
                "The function needs a catch block for errors.",
                "The await keyword is missing before fetch and response.json().",
                "The function should use XMLHttpRequest instead of fetch."
            ],
            correctAnswer: "The await keyword is missing before fetch and response.json().",
            explanation: "The fetch call and response.json() are asynchronous operations. Without the await keyword, the code moves on before the promises are resolved, causing undefined to be returned."
        },
        {
            language: "JavaScript",
            errorType: "Logical Error",
            mode: "multiple-choice",
            level: "intermediate",
            question: "This code is supposed to create a new array with numbers greater than 5, but it's not working. What is the bug? 🐞",
            brokenCode: "const numbers = [1, 7, 3, 9, 2, 8];\nconst filteredNumbers = numbers.forEach(num => {\n  if (num > 5) return num;\n});\nconsole.log(filteredNumbers); // Expected: [7, 9, 8]",
            options: [
                "The forEach method is misspelled.",
                "The arrow function needs curly braces.",
                "The return statement is not correctly used in forEach.",
                "The forEach method returns undefined and should be replaced with filter."
            ],
            correctAnswer: "The forEach method returns undefined and should be replaced with filter.",
            explanation: "The forEach method executes a function for each element but does not return a new array. The filter method is the correct choice here as it returns a new array containing only the elements that pass the provided test."
        },
        {
            language: "Python",
            errorType: "Logical Error",
            mode: "multiple-choice",
            level: "intermediate",
            question: "This Python function is supposed to return a new list with unique values, but it's not working correctly. Find the bug! 🐞",
            brokenCode: "def get_unique(arr):\n  unique = []\n  for item in arr:\n    if item not in unique:\n      unique.append(item)\n  return unique",
            options: [
                "The append method is incorrect.",
                "The function should use a set for better performance.",
                "The code is syntactically wrong.",
                "The variable unique is not initialized."
            ],
            correctAnswer: "The function should use a set for better performance.",
            explanation: "While the logic is correct, it is highly inefficient for large lists because item not in unique performs a linear scan. A more Pythonic and performant way to find unique elements is to use a set."
        },
        
        // --- Intermediate Fix the Bug Questions ---
        {
            language: "JavaScript",
            errorType: "Logical Error",
            mode: "fix-the-bug",
            level: "intermediate",
            question: "This function is meant to capitalize the first letter of each word in a string, but it's not working correctly. Fix it! 🐞",
            brokenCode: "function capitalizeWords(str) {\n  return str.split(' ').map(word => {\n    return word.charAt(0).toUpperCase();\n  }).join(' ');\n}",
            correctCode: "function capitalizeWords(str) {\n  return word.charAt(0).toUpperCase() + word.slice(1);",
            explanation: "The code was only returning the first character of each word, capitalized. It needs to also return the rest of the word, which can be achieved with word.slice(1)."
        },
        {
            language: "Python",
            errorType: "Logical Error",
            mode: "fix-the-bug",
            level: "intermediate",
            question: "This code is meant to check if a number is positive, but the output is always wrong. Fix the logic! 🐞",
            brokenCode: "num = -5\nif num > 0:\n    print('Positive')\nelse:\n    print('Negative')",
            correctCode: "num = -5\nif num > 0:\n    print('Positive')\nelif num < 0:\n    print('Negative')\nelse:\n    print('Zero')",
            explanation: "The 'else' block incorrectly assumes a number is negative if it is not positive. The code should explicitly handle the case of zero. A better 'else' message would be 'Not positive', but adding an elif for zero is a more robust fix."
        },
        {
            language: "JavaScript",
            errorType: "Type Coercion",
            mode: "fix-the-bug",
            level: "intermediate",
            question: "This code always logs 'Not Equal' even when the numbers are the same. Fix the comparison operator. 🐞",
            brokenCode: "let a = 5;\nlet b = \"5\";\n\nif (a == b) {\n  console.log('Equal');\n} else {\n  console.log('Not Equal');\n}",
            correctCode: "let a = 5;\nlet b = \"5\";\n\nif (a === b) {\n  console.log('Equal');\n} else {\n  console.log('Not Equal');\n}",
            explanation: "The == operator performs type coercion, meaning it tries to convert the values to the same type before comparing them. The === operator (strict equality) checks both value and type, which is what's needed here to correctly identify that the number 5 is not strictly equal to the string '5'."
        },
        
        // --- Advanced Multiple Choice Questions ---
        {
            language: "JavaScript",
            errorType: "Scope/Closure Bug",
            mode: "multiple-choice",
            level: "advanced",
            question: "Why does the following code always print '10' instead of the numbers 0 through 9? 🐞",
            brokenCode: "for (var i = 0; i < 10; i++) {\n  setTimeout(() => console.log(i), 100);\n}",
            options: [
                "The setTimeout function is being called incorrectly.",
                "The var keyword creates a function-scoped variable, which is modified by the loop before the timeouts execute.",
                "The console.log statement should be outside the loop.",
                "The loop condition i < 10 is incorrect."
            ],
            correctAnswer: "The var keyword creates a function-scoped variable, which is modified by the loop before the timeouts execute.",
            explanation: "The var keyword does not have block scope. By the time the setTimeout callbacks finally run (after the loop has finished), the variable i has already been incremented to its final value of 10. To fix this, you should use let instead of var to create a block-scoped variable for each iteration."
        },
        {
            language: "C#",
            errorType: "Concurrency Bug",
            mode: "multiple-choice",
            level: "advanced",
            question: "This C# code has a race condition. What is the best way to ensure the final value of the counter is always 1000? 🐞",
            brokenCode: "int counter = 0;\nList<Task> tasks = new List<Task>();\nfor (int i = 0; i < 1000; i++) {\n  tasks.Add(Task.Run(() => counter++));\n}\nTask.WhenAll(tasks).Wait();\nConsole.WriteLine(counter);",
            options: [
                "Use a lock statement or Interlocked.Increment.",
                "The Task.WhenAll method should be replaced with a for loop.",
                "The code is fine and does not need to be fixed.",
                "The counter variable should be a string."
            ],
            correctAnswer: "Use a lock statement or Interlocked.Increment.",
            explanation: "The counter++ operation is not atomic, meaning multiple threads can read, increment, and write to the variable at the same time, leading to lost updates. Using a lock or the Interlocked.Increment method ensures that only one thread can access the counter at a time, preventing race conditions."
        },
        {
            language: "JavaScript",
            errorType: "Logical Error",
            mode: "multiple-choice",
            level: "advanced",
            question: "This function is designed to recursively flatten a nested array, but it only works on the first level. What's the bug? 🐞",
            brokenCode: "function flattenArray(arr) {\n  let flattened = [];\n  arr.forEach(item => {\n    if (Array.isArray(item)) {\n      item.forEach(subItem => flattened.push(subItem));\n    } else {\n      flattened.push(item);\n    }\n  });\n  return flattened;\n}",
            options: [
                "The forEach method is incorrect for this task.",
                "The function needs to be recursive.",
                "The flattened array is not initialized correctly.",
                "The Array.isArray check is wrong."
            ],
            correctAnswer: "The function needs to be recursive.",
            explanation: "The function only checks for arrays at the first level. To handle deeply nested arrays, the function must call itself (flattenArray) on any sub-array it finds. The logic should be item.forEach(subItem => flattened.push(...flattenArray([subItem]))); or a similar recursive call."
        },
        
        // --- Advanced Fix the Bug Questions ---
        {
            language: "Python",
            errorType: "Performance Bug",
            mode: "fix-the-bug",
            level: "advanced",
            question: "This function is designed to find the largest number in a list, but for very large lists, it is inefficient. Optimize it for better performance. 🐞",
            brokenCode: "def find_largest(numbers):\n    if not numbers: return None\n    largest = numbers[0]\n    for num in numbers:\n        if num > largest:\n            largest = num\n    return largest",
            correctCode: "def find_largest(numbers):\n    if not numbers: return None\n    return max(numbers)",
            explanation: "The built-in max() function in Python is highly optimized and much faster for finding the largest element in a collection than a manual loop, especially for large datasets. The original code is correct but inefficient."
        },
        {
            language: "JavaScript",
            errorType: "Logical Error",
            mode: "fix-the-bug",
            level: "advanced",
            question: "This function is supposed to return a new array with only unique values, but it's not working correctly. Fix it! 🐞",
            brokenCode: "function getUnique(arr) {\n  let unique = [];\n  for (let i = 0; i < arr.length; i++) {\n    if (arr.indexOf(arr[i]) === i) {\n      unique.push(arr[i]);\n    }\n  }\n  return unique;\n}",
            correctCode: "function getUnique(arr) {\n  return [...new Set(arr)];\n}",
            explanation: "The indexOf method always returns the index of the first occurrence of an element. This logic fails for arrays with duplicates. A much cleaner, more efficient way to get unique values is to convert the array to a Set (which only stores unique values) and then spread it back into an array."
        },
        {
            language: "Python",
            errorType: "Concurrency Bug",
            mode: "fix-the-bug",
            level: "advanced",
            question: "This Python code has a race condition. It's supposed to use multiprocessing to increment a shared counter 1000 times, but the final value is not always 1000. Fix the code to ensure the final value is always correct. 🐞",
            brokenCode: "import multiprocessing\n\ndef increment(counter):\n    for _ in range(100):\n        counter.value += 1\n\nif _name_ == '_main_':\n    counter = multiprocessing.Value('i', 0)\n    processes = []\n    for _ in range(10):\n        p = multiprocessing.Process(target=increment, args=(counter,))\n        processes.append(p)\n        p.start()\n    for p in processes:\n        p.join()\n    print(counter.value)",
            correctCode: "import multiprocessing\n\ndef increment(counter, lock):\n    for _ in range(100):\n        with lock:\n            counter.value += 1\n\nif _name_ == '_main_':\n    counter = multiprocessing.Value('i', 0)\n    lock = multiprocessing.Lock()\n    processes = []\n    for _ in range(10):\n        p = multiprocessing.Process(target=increment, args=(counter, lock))\n        processes.append(p)\n        p.start()\n    for p in processes:\n        p.join()\n    print(counter.value)",
            explanation: "The original code has a race condition because multiple processes try to increment the shared counter at the same time. The counter.value += 1 operation is not atomic. The solution is to use a Lock to ensure that only one process can access and modify the shared counter at any given moment, ensuring atomicity and correctness."
        },
    ];

    // --- Game State Variables ---
    let playerName = 'Guest';
    let currentLevel = 'beginner';
    let currentGameMode = 'multiple-choice';
    let currentQuestionIndex = 0;
    let filteredQuestions = [];
    let score = 0;
    let timer;
    let timeLeft = 60;

    // --- DOM Elements ---
    const authScreen = document.getElementById('auth-screen');
    const startScreen = document.getElementById('start-screen');
    const modeScreen = document.getElementById('mode-screen');
    const gameScreen = document.getElementById('game-screen');
    const endScreen = document.getElementById('end-screen');
    const feedbackArea = document.getElementById('feedback-area');
    const questionTitle = document.getElementById('question-title');
    const questionText = document.getElementById('question-text');
    const codeDisplay = document.getElementById('code-display');
    const fixCodeEditor = document.getElementById('fix-code-editor');
    const mcqOptions = document.getElementById('mcq-options');
    const scoreBoard = document.querySelector('#score-board span');
    const timerDisplay = document.querySelector('#timer-display span');
    const finalScoreDisplay = document.getElementById('final-score');
    const hintModal = document.getElementById('hint-modal');
    const hintModalText = document.getElementById('hint-modal-text');
    const messageModal = document.getElementById('message-modal');
    const messageModalTitle = document.getElementById('message-modal-title');
    const messageModalText = document.getElementById('message-modal-text');
    const shareModal = document.getElementById('share-modal');
    const shareLinkInput = document.getElementById('share-link-input');

    // --- Game Flow Functions ---

    window.setNameAndProceed = function() {
      const nameInput = document.getElementById('player-name-input').value.trim();
      if (nameInput) {
        playerName = nameInput;
        showScreen(startScreen);
      } else {
        showMessageModal('Oops!', 'Please enter your name to start the game.');
      }
    }

    window.selectLevel = function(level) {
      currentLevel = level;
      showScreen(modeScreen);
    }

    window.startGame = function(mode) {
      currentGameMode = mode;
      score = 0;
      currentQuestionIndex = 0;
      filteredQuestions = shuffleArray(quizData.filter(q => q.level === currentLevel && q.mode === currentGameMode));

      if (filteredQuestions.length === 0) {
        showMessageModal('No Questions Found', 'There are no questions for this combination of level and mode. Please go back and try a different combination.');
        showScreen(startScreen);
        return;
      }

      if (currentGameMode === 'time-attack') {
        timeLeft = 60;
        timerDisplay.textContent = timeLeft;
        startTimer();
        document.getElementById('timer-display').style.display = 'block';
        document.getElementById('submit-btn').textContent = 'Next';
      } else {
        document.getElementById('timer-display').style.display = 'none';
        document.getElementById('submit-btn').textContent = 'Submit';
      }
      showScreen(gameScreen);
      loadQuestion();
    }

    window.loadQuestion = function() {
      if (currentQuestionIndex >= filteredQuestions.length) {
        endGame();
        return;
      }

      feedbackArea.style.display = 'none';
      document.getElementById('next-btn').style.display = 'none';

      const currentQuestion = filteredQuestions[currentQuestionIndex];
      questionTitle.textContent = ${currentQuestion.language} - ${currentQuestion.errorType} 🐞;
      questionText.textContent = currentQuestion.question;
      codeDisplay.textContent = currentQuestion.brokenCode;

      mcqOptions.style.display = 'none';
      fixCodeEditor.style.display = 'none';
      document.getElementById('submit-btn').style.display = 'block';
      document.getElementById('hint-btn').style.display = 'block';

      if (currentGameMode === 'multiple-choice' || currentGameMode === 'time-attack') {
        mcqOptions.style.display = 'flex';
        renderMcqOptions(currentQuestion.options);
      } else if (currentGameMode === 'fix-the-bug') {
        fixCodeEditor.style.display = 'block';
        fixCodeEditor.value = currentQuestion.brokenCode;
      }
    }

    window.checkAnswer = function() {
      const currentQuestion = filteredQuestions[currentQuestionIndex];
      let isCorrect = false;
      let userAnswer = '';

      if (currentGameMode === 'multiple-choice' || currentGameMode === 'time-attack') {
        const selectedOption = document.querySelector('input[name="mcq-option"]:checked');
        if (selectedOption) {
          userAnswer = selectedOption.value;
          isCorrect = userAnswer === currentQuestion.correctAnswer;
        } else {
          showMessageModal('No selection', 'Please select an answer before submitting.');
          return;
        }
      } else if (currentGameMode === 'fix-the-bug') {
        userAnswer = fixCodeEditor.value;
        isCorrect = normalizeCode(userAnswer) === normalizeCode(currentQuestion.correctCode);
      }

      if (isCorrect) {
        score++;
        scoreBoard.textContent = score;
        showFeedback('Correct! 🎉', currentQuestion.explanation, true, currentQuestion);
      } else {
        showFeedback('Incorrect. 😔', currentQuestion.explanation, false, currentQuestion);
      }

      document.getElementById('submit-btn').style.display = 'none';
      document.getElementById('hint-btn').style.display = 'none';
    }

    window.endGame = function() {
      clearInterval(timer);
      showScreen(endScreen);
      const finalScore = currentGameMode === 'time-attack' ? score : ${score} / ${filteredQuestions.length};
      finalScoreDisplay.textContent = ${playerName}'s Final Score: ${finalScore};

      // Placeholder for a more complex reward system
      const rewards = [
        { score: 10, message: "You're a debugging master!", tip: "Always check your variable names." },
        { score: 5, message: "Great job, you're getting the hang of it!", tip: "Read error messages carefully; they often point you to the right line." },
        { score: 0, message: "Keep practicing, you'll get there!", tip: "Don't be afraid to use console.log() to see what's happening." }
      ];
      const reward = rewards.find(r => score >= r.score) || rewards[rewards.length - 1];
      document.getElementById('reward-message').textContent = reward.message;
      document.getElementById('coding-tip').textContent = Tip of the day: ${reward.tip};
    }

    window.nextQuestion = function() {
      currentQuestionIndex++;
      loadQuestion();
    }

    window.resetGame = function() {
      clearInterval(timer);
      showScreen(authScreen);
    }

    window.showHint = function() {
      const currentQuestion = filteredQuestions[currentQuestionIndex];
      if (currentQuestion && currentQuestion.explanation) {
        hintModalText.textContent = currentQuestion.explanation;
        hintModal.style.display = 'flex';
      } else {
        showMessageModal('No Hint', 'No hint available for this question.');
      }
    }

    window.closeHintModal = function() {
      hintModal.style.display = 'none';
    }

    window.showShareModal = function() {
      const shareLink = window.location.href.split('?')[0]; // Simple share link
      shareLinkInput.value = shareLink;
      shareModal.style.display = 'flex';
    }

    window.closeShareModal = function() {
      shareModal.style.display = 'none';
    }

    window.copyToClipboard = function() {
      shareLinkInput.select();
      document.execCommand('copy');
      showMessageModal('Copied!', 'The link has been copied to your clipboard.');
    }

    // --- Utility Functions ---
    function showScreen(screen) {
      document.querySelectorAll('.selection-screen, .game-area').forEach(s => s.style.display = 'none');
      screen.style.display = 'flex';
    }

    function shuffleArray(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    }

    function renderMcqOptions(options) {
      mcqOptions.innerHTML = '';
      options.forEach((option, index) => {
        const optionHtml = `
          <div class="mcq-option">
            <input type="radio" id="option${index}" name="mcq-option" value="${option}">
            <label for="option${index}" class="px-4 py-2">${option}</label>
          </div>
        `;
        mcqOptions.innerHTML += optionHtml;
      });
    }

    function showFeedback(title, explanation, isCorrect, question) {
      feedbackArea.classList.remove('correct', 'incorrect');
      feedbackArea.classList.add(isCorrect ? 'correct' : 'incorrect');
      document.getElementById('feedback-title').textContent = title;
      document.getElementById('explanation').textContent = explanation;

      if (currentGameMode === 'fix-the-bug' && !isCorrect) {
        document.getElementById('diff-view').style.display = 'block';
        document.getElementById('broken-code-diff').textContent = question.brokenCode;
        document.getElementById('fixed-code-diff').textContent = question.correctCode;
      } else {
        document.getElementById('diff-view').style.display = 'none';
      }

      feedbackArea.style.display = 'block';
      document.getElementById('next-btn').style.display = 'block';
    }

    function normalizeCode(code) {
      return code.replace(/\s+/g, ' ').trim();
    }

    function startTimer() {
      clearInterval(timer);
      timer = setInterval(() => {
        timeLeft--;
        timerDisplay.textContent = timeLeft;
        if (timeLeft <= 0) {
          endGame();
        }
      }, 1000);
    }

    window.showMessageModal = function(title, text) {
      messageModalTitle.textContent = title;
      messageModalText.textContent = text;
      messageModal.style.display = 'flex';
    }

    window.closeMessageModal = function() {
      messageModal.style.display = 'none';
    }
</script>
</body>
</html>
