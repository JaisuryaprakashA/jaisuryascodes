import React, { useState, useEffect, useRef } from 'react';
// Chart.js is loaded via CDN in public/index.html, so we access it via window.Chart

// Main App component
const App = () => {
    // Historical data for temperature and ice cream sales
    const historicalData = {
        temperature: [20, 22, 18, 25, 23, 19],
        sales: [100, 120, 80, 150, 130, 90],
    };

    // State variables
    const [temperatureInput, setTemperatureInput] = useState('');
    const [predictedSales, setPredictedSales] = useState(null);
    const [slope, setSlope] = useState(0);
    const [intercept, setIntercept] = useState(0);
    const [errorMessage, setErrorMessage] = useState('');

    // Ref for the chart canvas element
    const chartRef = useRef(null);
    // Ref to store the Chart.js instance
    const chartInstance = useRef(null);

    // Function to calculate linear regression coefficients (slope and intercept)
    const calculateLinearRegression = (x, y) => {
        const n = x.length;
        let sumX = 0;
        let sumY = 0;
        let sumXY = 0;
        let sumXX = 0;

        // Calculate sums
        for (let i = 0; i < n; i++) {
            sumX += x[i];
            sumY += y[i];
            sumXY += x[i] * y[i];
            sumXX += x[i] * x[i];
        }

        // Calculate means
        const meanX = sumX / n;
        const meanY = sumY / n;

        // Calculate slope (b1)
        const numerator = sumXY - n * meanX * meanY;
        const denominator = sumXX - n * meanX * meanX;

        // Handle cases where denominator is zero (e.g., all X values are the same)
        if (denominator === 0) {
            console.error("Denominator is zero, cannot calculate slope. All temperature values might be the same.");
            return { slope: NaN, intercept: NaN };
        }

        const calculatedSlope = numerator / denominator;

        // Calculate intercept (b0)
        const calculatedIntercept = meanY - calculatedSlope * meanX;

        return { slope: calculatedSlope, intercept: calculatedIntercept };
    };

    // useEffect hook to calculate slope and intercept when the component mounts
    useEffect(() => {
        const { slope, intercept } = calculateLinearRegression(
            historicalData.temperature,
            historicalData.sales
        );
        setSlope(slope);
        setIntercept(intercept);
    }, []); // Empty dependency array means this runs once on mount

    // useEffect hook to initialize and update the Chart.js visualization
    useEffect(() => {
        // Ensure Chart.js is loaded and available on the window object
        if (chartRef.current && window.Chart) {
            const ctx = chartRef.current.getContext('2d');

            // Prepare data points for the scatter plot
            const chartDataPoints = historicalData.temperature.map((temp, index) => ({
                x: temp,
                y: historicalData.sales[index],
            }));

            // Prepare data points for the regression line
            // The line should span the min and max temperature values in the historical data
            const minTemp = Math.min(...historicalData.temperature);
            const maxTemp = Math.max(...historicalData.temperature);
            const lineDataPoints = [
                { x: minTemp, y: slope * minTemp + intercept },
                { x: maxTemp, y: slope * maxTemp + intercept },
            ];

            // If a chart instance already exists, destroy it to prevent memory leaks
            if (chartInstance.current) {
                chartInstance.current.destroy();
            }

            // Create a new Chart.js instance
            chartInstance.current = new window.Chart(ctx, { // Use window.Chart
                type: 'scatter', // Use scatter type for the main data points
                data: {
                    datasets: [
                        {
                            label: 'Actual Sales Data',
                            data: chartDataPoints,
                            backgroundColor: 'rgba(75, 192, 192, 0.8)', // Color for data points
                            borderColor: 'rgba(75, 192, 192, 1)',
                            pointRadius: 6, // Size of data points
                            pointHoverRadius: 8,
                        },
                        {
                            label: `Regression Line (Sales = ${slope.toFixed(2)} * Temp + ${intercept.toFixed(2)})`,
                            data: lineDataPoints,
                            type: 'line', // This dataset will be rendered as a line
                            borderColor: 'red',
                            backgroundColor: 'transparent',
                            borderWidth: 2,
                            borderDash: [5, 5], // Dashed line style
                            pointRadius: 0, // No points on the regression line itself
                            fill: false,
                        },
                    ],
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false, // Allow canvas to resize freely
                    plugins: {
                        title: {
                            display: true,
                            text: 'Ice Cream Sales vs. Temperature',
                            font: {
                                size: 18,
                            },
                        },
                        legend: {
                            position: 'top',
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    if (context.datasetIndex === 0) { // For actual data points
                                        return `Temp: ${context.parsed.x}°C, Sales: ${context.parsed.y} cones`;
                                    }
                                    return context.dataset.label; // For regression line
                                }
                            }
                        }
                    },
                    scales: {
                        x: {
                            type: 'linear', // Linear scale for temperature
                            position: 'bottom',
                            title: {
                                display: true,
                                text: 'Temperature (°C)',
                                font: {
                                    size: 14,
                                },
                            },
                        },
                        y: {
                            title: {
                                display: true,
                                text: 'Ice Cream Sales (Cones)',
                                font: {
                                    size: 14,
                                },
                            },
                            beginAtZero: true, // Start y-axis at zero
                        },
                    },
                },
            });
        }
    }, [slope, intercept, historicalData]); // Re-run effect if slope, intercept, or historical data changes

    // Handle input change
    const handleInputChange = (e) => {
        setTemperatureInput(e.target.value);
        setErrorMessage(''); // Clear error message on input change
    };

    // Handle prediction button click
    const handlePredict = () => {
        const temp = parseFloat(temperatureInput);

        if (isNaN(temp)) {
            setErrorMessage('Please enter a valid number for temperature.');
            setPredictedSales(null);
            return;
        }

        // Calculate predicted sales using the linear regression equation
        const prediction = slope * temp + intercept;
        setPredictedSales(prediction.toFixed(2)); // Round to two decimal places
        setErrorMessage('');
    };

    return (
        <div className="min-h-screen bg-gradient-to-br from-blue-100 to-purple-100 flex flex-col items-center justify-center p-4 font-sans text-gray-800">
            <script src="https://cdn.tailwindcss.com"></script>
            {/* Moved Chart.js script to head or before React app for global availability */}
            <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
            <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet" />

            <style>
                {`
                body {
                    font-family: 'Inter', sans-serif;
                }
                .chart-container {
                    position: relative;
                    height: 400px; /* Fixed height for the chart */
                    width: 100%;
                    max-width: 800px;
                }
                `}
            </style>

            <div className="bg-white p-8 rounded-xl shadow-2xl w-full max-w-2xl text-center border-b-4 border-blue-500">
                <h1 className="text-4xl font-bold text-blue-700 mb-6">
                    Ice Cream Sales Predictor
                </h1>

                <p className="text-lg text-gray-600 mb-8">
                    Enter a temperature to predict the number of ice cream cones you might sell.
                </p>

                <div className="flex flex-col sm:flex-row items-center justify-center gap-4 mb-8">
                    <input
                        type="number"
                        value={temperatureInput}
                        onChange={handleInputChange}
                        placeholder="Enter Temperature (°C)"
                        className="p-3 border-2 border-blue-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-400 w-full sm:w-64 text-lg"
                    />
                    <button
                        onClick={handlePredict}
                        className="bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 px-6 rounded-lg shadow-md transition duration-300 ease-in-out transform hover:scale-105 w-full sm:w-auto text-lg"
                    >
                        Predict Sales
                    </button>
                </div>

                {errorMessage && (
                    <p className="text-red-600 text-md mb-4">{errorMessage}</p>
                )}

                {predictedSales !== null && (
                    <div className="bg-blue-50 p-4 rounded-lg border border-blue-200 mb-8">
                        <p className="text-xl font-semibold text-blue-800">
                            Predicted Ice Cream Sales: <span className="text-blue-600 text-2xl">{predictedSales} cones</span>
                        </p>
                    </div>
                )}

                <div className="chart-container mx-auto">
                    <canvas ref={chartRef}></canvas>
                </div>

                <div className="mt-8 text-left text-gray-700">
                    <h2 className="text-2xl font-semibold text-blue-700 mb-3">
                        Model Details:
                    </h2>
                    <p className="text-lg">
                        Based on historical data, the linear regression model is:
                    </p>
                    <p className="text-xl font-bold text-blue-800 mt-2">
                        Sales = {slope.toFixed(2)} * Temperature + {intercept.toFixed(2)}
                    </p>
                    <ul className="list-disc list-inside mt-4 text-md">
                        <li>
                            **Slope ({slope.toFixed(2)}):** For every 1$^\circ$C increase in temperature, ice cream sales are predicted to increase by {slope.toFixed(2)} cones.
                        </li>
                        <li>
                            **Intercept ({intercept.toFixed(2)}):** This is the theoretical sales at 0$^\circ$C.
                        </li>
                    </ul>
                </div>
            </div>
        </div>
    );
};

export default App;
