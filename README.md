<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Diyora's Grade Summary</title>
    <link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;500;600;700&display=swap" rel="stylesheet" />
    <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400;500;600&display=swap" rel="stylesheet" />
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Montserrat', sans-serif;
        }

        :root {
            --primary: #a770ff;
            --primary-hover: #cf8fff;
            --bg-dark: #0f0f1e;
            --text-light: rgba(255, 255, 255, 0.8);
            --text-mid: rgba(255, 255, 255, 0.6);
            --card-bg: rgba(15, 15, 30, 0.5);
        }
        
        body {
            background: var(--bg-dark);
            background-image: 
                radial-gradient(circle at 20% 30%, rgba(76, 0, 255, 0.15) 0%, transparent 50%),
                radial-gradient(circle at 80% 70%, rgba(255, 0, 128, 0.15) 0%, transparent 50%);
            color: white;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            overflow-x: hidden;
            position: relative;
        }

        /* Animated background */
        .animated-bg {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            z-index: -2;
            overflow: hidden;
        }

        .animated-bg .bubble {
            position: absolute;
            border-radius: 50%;
            opacity: 0.1;
            filter: blur(20px);
            animation: float 15s infinite linear;
        }

        @keyframes float {
            0% { transform: translateY(0) rotate(0deg); }
            25% { transform: translateY(-50px) rotate(90deg); }
            50% { transform: translateY(0) rotate(180deg); }
            75% { transform: translateY(50px) rotate(270deg); }
            100% { transform: translateY(0) rotate(360deg); }
        }
        
        .navbar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 25px;
            background: rgba(255, 255, 255, 0.03);
            backdrop-filter: blur(12px);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
            position: relative;
            z-index: 10;
            border-bottom: 1px solid rgba(255, 255, 255, 0.05);
        }
        
        .navbar h1 {
            font-family: 'Syne', sans-serif;
            font-size: 28px;
            letter-spacing: 2px;
            font-weight: 600;
            text-transform: uppercase;
            background: linear-gradient(90deg, #f0f0f0, var(--primary));
            background-clip: text;
            -webkit-background-clip: text;
            color: transparent;
            position: relative;
        }

        .back-button {
            color: var(--text-light);
            text-decoration: none;
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 16px;
            font-weight: 500;
            transition: all 0.3s ease;
            background: rgba(255, 255, 255, 0.05);
            padding: 8px 16px;
            border-radius: 8px;
            border: 1px solid rgba(255, 255, 255, 0.1);
        }

        .back-button:hover {
            background: rgba(167, 112, 255, 0.2);
            transform: translateY(-2px);
        }
        
        .container {
            max-width: 1100px;
            margin: 40px auto;
            padding: 0 30px;
            flex-grow: 1;
            position: relative;
        }
        
        .card {
            background: var(--card-bg);
            border-radius: 20px;
            overflow: hidden;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.25);
            transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            border: 1px solid rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(10px);
            position: relative;
            margin-bottom: 40px;
        }
        
        .card::before {
            content: '';
            position: absolute;
            top: -2px;
            left: -2px;
            right: -2px;
            bottom: -2px;
            background: linear-gradient(45deg, #ff00cc, #3333ff, #00ffff, #ff00cc);
            z-index: -1;
            border-radius: 21px;
            background-size: 400%;
            opacity: 0;
            transition: opacity 0.4s ease;
        }
        
        .card:hover::before {
            opacity: 0.5;
            animation: glowingBorder 8s linear infinite;
        }
        
        @keyframes glowingBorder {
            0% { background-position: 0 0; }
            50% { background-position: 400% 0; }
            100% { background-position: 0 0; }
        }
        
        .card-header {
            background: rgba(0, 0, 0, 0.2);
            color: white;
            padding: 25px 30px;
            font-size: 22px;
            font-weight: 700;
            letter-spacing: 1px;
            position: relative;
            overflow: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
            font-family: 'Syne', sans-serif;
        }
        
        .card-header::after {
            content: '';
            position: absolute;
            bottom: 0;
            left: 0;
            right: 0;
            height: 1px;
            background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.3), transparent);
        }
        
        .card-body {
            padding: 35px;
            color: var(--text-light);
        }

        /* Grade Summary Section */
        .grade-summary {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 30px;
            padding: 20px;
            background: rgba(0, 0, 0, 0.2);
            border-radius: 12px;
            border: 1px solid rgba(255, 255, 255, 0.05);
        }

        .grade-info {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .grade-title {
            font-size: 18px;
            color: var(--text-light);
            font-weight: 600;
        }

        .grade-value {
            font-size: 42px;
            font-weight: 700;
            color: var(--primary);
            font-family: 'Syne', sans-serif;
        }

        .grade-detail {
            font-size: 16px;
            color: var(--text-mid);
        }

        .grade-chart {
            width: 180px;
            height: 180px;
            position: relative;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .grade-circle {
            width: 100%;
            height: 100%;
            border-radius: 50%;
            background: conic-gradient(var(--primary) 0% 90%, rgba(255, 255, 255, 0.1) 90% 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            position: relative;
        }

        .grade-circle::before {
            content: '';
            position: absolute;
            width: 80%;
            height: 80%;
            border-radius: 50%;
            background: var(--bg-dark);
        }

        .grade-circle-text {
            position: absolute;
            font-size: 32px;
            font-weight: 700;
            color: white;
            font-family: 'Syne', sans-serif;
        }
        
        /* Grades Table */
        .grades-table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
            background: rgba(0, 0, 0, 0.1);
            border-radius: 10px;
            overflow: hidden;
        }

        .grades-table th,
        .grades-table td {
            padding: 15px 20px;
            text-align: left;
            border-bottom: 1px solid rgba(255, 255, 255, 0.05);
        }

        .grades-table th {
            background: rgba(0, 0, 0, 0.2);
            color: white;
            font-weight: 600;
            text-transform: uppercase;
            font-size: 14px;
            letter-spacing: 1px;
        }

        .grades-table tr:last-child td {
            border-bottom: none;
        }

        .grades-table tr:hover {
            background: rgba(255, 255, 255, 0.03);
        }

        .grade-status {
            padding: 6px 12px;
            border-radius: 50px;
            font-size: 12px;
            font-weight: 600;
            text-align: center;
            display: inline-block;
            min-width: 100px;
        }

        .status-completed {
            background: rgba(46, 213, 115, 0.15);
            color: #2ed573;
            border: 1px solid rgba(46, 213, 115, 0.3);
        }

        .status-pending {
            background: rgba(255, 159, 67, 0.15);
            color: #ff9f43;
            border: 1px solid rgba(255, 159, 67, 0.3);
        }

        .status-missed {
            background: rgba(255, 71, 87, 0.15);
            color: #ff4757;
            border: 1px solid rgba(255, 71, 87, 0.3);
        }

        /* Grade Chart */
        .grade-distribution {
            margin-top: 40px;
        }

        .grade-chart-container {
            background: rgba(0, 0, 0, 0.2);
            padding: 20px;
            border-radius: 12px;
            border: 1px solid rgba(255, 255, 255, 0.05);
            margin-top: 20px;
            height: 300px;
            position: relative;
        }

        .bar-chart {
            display: flex;
            height: 220px;
            align-items: flex-end;
            gap: 20px;
            padding-bottom: 30px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
            justify-content: space-around;
            position: relative;
        }

        .bar-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 100%;
            position: relative;
        }

        .bar {
            width: 40px;
            background: linear-gradient(to top, var(--primary), var(--primary-hover));
            border-radius: 8px 8px 0 0;
            position: relative;
            transition: height 1s ease;
        }

        .bar-label {
            position: absolute;
            bottom: -25px;
            font-size: 12px;
            color: var(--text-mid);
            white-space: nowrap;
        }

        .bar-value {
            position: absolute;
            bottom: calc(100% + 5px);
            font-size: 12px;
            font-weight: 600;
            color: white;
        }

        /* Legend */
        .chart-legend {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin-top: 20px;
        }

        .legend-item {
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 14px;
            color: var(--text-mid);
        }

        .legend-color {
            width: 12px;
            height: 12px;
            border-radius: 3px;
        }

        .legend-assignments {
            background: var(--primary);
        }

        /* Y-axis labels */
        .y-axis {
            position: absolute;
            left: 0;
            top: 0;
            height: 220px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            padding: 0 10px;
        }

        .y-label {
            font-size: 12px;
            color: var(--text-mid);
            transform: translateY(-50%);
        }

        footer {
            background: rgba(0, 0, 0, 0.3);
            padding: 25px;
            text-align: center;
            margin-top: auto;
            font-size: 14px;
            letter-spacing: 1px;
            color: var(--text-mid);
            backdrop-filter: blur(10px);
            border-top: 1px solid rgba(255, 255, 255, 0.03);
        }

        /* Media Queries */
        @media (max-width: 768px) {
            .grade-summary {
                flex-direction: column;
                gap: 30px;
            }

            .grade-chart {
                margin: 0 auto;
            }

            .bar {
                width: 25px;
            }

            .bar-label {
                font-size: 10px;
                transform: rotate(-45deg);
                transform-origin: top left;
                left: 12px;
                bottom: -20px;
            }
        }
    </style>
    <script>
        document.addEventListener('DOMContentLoaded', function() {
            // Create animated background bubbles
            const animatedBg = document.querySelector('.animated-bg');
            const colors = ['#a770ff', '#ff00cc', '#3333ff', '#00ffff'];
            
            for (let i = 0; i < 20; i++) {
                const bubble = document.createElement('div');
                bubble.classList.add('bubble');
                
                const size = Math.random() * 200 + 50;
                const color = colors[Math.floor(Math.random() * colors.length)];
                
                bubble.style.width = `${size}px`;
                bubble.style.height = `${size}px`;
                bubble.style.left = `${Math.random() * 100}%`;
                bubble.style.top = `${Math.random() * 100}%`;
                bubble.style.backgroundColor = color;
                bubble.style.animationDelay = `${Math.random() * 15}s`;
                bubble.style.animationDuration = `${15 + Math.random() * 10}s`;
                
                animatedBg.appendChild(bubble);
            }

            // Animate the bars in the chart
            setTimeout(() => {
                document.getElementById('bar1').style.height = '180px';
                document.getElementById('bar2').style.height = '150px';
                document.getElementById('bar3').style.height = '200px';
                document.getElementById('bar4').style.height = '130px';
            }, 500);

            // Fetch grades data
            fetchGrades();
        });
        
        // Function to fetch grades from Google Script
        function fetchGrades() {
            const gradesUrl = "https://script.google.com/macros/s/AKfycbzl5muotqZ6wYv5LKIf_fHQ6d46Qday8QAKqLc8H9phdKibwJ7UBdDp3o2-xxC4lkNnqg/exec";
            
            fetch(gradesUrl)
                .then(response => {
                    if (!response.ok) {
                        throw new Error('Network response was not ok');
                    }
                    return response.json();
                })
                .then(data => {
                    // Update with actual data if available
                    console.log("Grades data:", data);
                })
                .catch(error => {
                    console.error('Error fetching grades:', error);
                });
        }
    </script>
</head>

<body>
    <div class="animated-bg"></div>
    
    <div class="navbar">
        <a href="https://dioram8.github.io/website/" class="back-button">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <line x1="19" y1="12" x2="5" y2="12"></line>
                <polyline points="12 19 5 12 12 5"></polyline>
            </svg>
            Back to Portal
        </a>
        <h1>Grade Summary</h1>
        <div style="width: 120px;"></div> <!-- Empty div for flex spacing -->
    </div>
    
    <div class="container">
        <!-- Overall Grade Summary Card -->
        <div class="card">
            <div class="card-header">Overall Performance</div>
            <div class="card-body">
                <div class="grade-summary">
                    <div class="grade-info">
                        <div class="grade-title">Current Grade</div>
                        <div class="grade-value">90.32%</div>
                        <div class="grade-detail">29.00 / 32.00 points</div>
                    </div>
                    <div class="grade-chart">
                        <div class="grade-circle">
                            <div class="grade-circle-text">90%</div>
                        </div>
                    </div>
                </div>

                <h3>Detailed Score Breakdown</h3>
                <table class="grades-table">
                    <thead>
                        <tr>
                            <th>Assignment</th>
                            <th>Due Date</th>
                            <th>Points</th>
                            <th>Status</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td>Assignment 1</td>
                            <td>Feb 15, 2025</td>
                            <td>2.25 / 3.0</td>
                            <td><span class="grade-status status-completed">Completed</span></td>
                        </tr>
                        <tr>
                            <td>Assignment 2</td>
                            <td>Mar 1, 2025</td>
                            <td>2.75 / 3.0</td>
                            <td><span class="grade-status status-completed">Completed</span></td>
                        </tr>
                        <tr>
                            <td>Quiz 1</td>
                            <td>Mar 15, 2025</td>
                            <td>3.0 / 5.0</td>
                            <td><span class="grade-status status-completed">Completed</span></td>
                        </tr>
                        <tr>
                            <td>Assignment 3</td>
                            <td>Apr 1, 2025</td>
                            <td>3.0 / 3.0</td>
                            <td><span class="grade-status status-completed">Completed</span></td>
                        </tr>
                        <tr>
                            <td>Assignment 4</td>
                            <td>Apr 15, 2025</td>
                            <td>0.0 / 3.0</td>
                            <td><span class="grade-status status-pending">Pending</span></td>
                        </tr>
                        <tr>
                            <td>Assignment 5</td>
                            <td>May 1, 2025</td>
                            <td>0.0 / 3.0</td>
                            <td><span class="grade-status status-pending">Pending</span></td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>

        <!-- Grade Distribution Chart -->
        <div class="card grade-distribution">
            <div class="card-header">Assignment Score Distribution</div>
            <div class="card-body">
                <div class="grade-chart-container">
                    <div class="y-axis">
                        <div class="y-label">100%</div>
                        <div class="y-label">75%</div>
                        <div class="y-label">50%</div>
                        <div class="y-label">25%</div>
                        <div class="y-label">0%</div>
                    </div>
                    <div class="bar-chart">
                        <div class="bar-container">
                            <div id="bar1" class="bar" style="height: 0px;"></div>
                            <div class="bar-value">75%</div>
                            <div class="bar-label">Assignment 1</div>
                        </div>
                        <div class="bar-container">
                            <div id="bar2" class="bar" style="height: 0px;"></div>
                            <div class="bar-value">91.67%</div>
                            <div class="bar-label">Assignment 2</div>
                        </div>
                        <div class="bar-container">
                            <div id="bar3" class="bar" style="height: 0px;"></div>
                            <div class="bar-value">60%</div>
                            <div class="bar-label">Quiz 1</div>
                        </div>
                        <div class="bar-container">
                            <div id="bar4" class="bar" style="height: 0px;"></div>
                            <div class="bar-value">100%</div>
                            <div class="bar-label">Assignment 3</div>
                        </div>
                    </div>
                </div>
                <div class="chart-legend">
                    <div class="legend-item">
                        <div class="legend-color legend-assignments"></div>
                        <span>Assignment Score</span>
                    </div>
                </div>
            </div>
        </div>
    </div>
    
    <footer>
        &copy; 2025 Diyora | Dong-A University
    </footer>
</body>
</html>
