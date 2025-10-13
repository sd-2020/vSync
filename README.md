"# vSync" 

import React, { useState } from "react";

const DonutChart = ({ labels = [], values = [], size = 2, colors }) => {
  const total = values.reduce((acc, val) => acc + val, 0);
  const percentages = values.map((v) => (v / total) * 100);
  const radius = 45;
  const circumference = 2 * Math.PI * radius;
  const scaledSize = size * 100;
  const defaultColors = ["#4CAF50", "#F44336", "#2196F3", "#FFC107", "#9C27B0"];
  const colorSet = colors || defaultColors;

  const [hoveredIndex, setHoveredIndex] = useState(null);
  let cumulative = 0;

  return (
    <div className="flex flex-col items-center">
      <div
        className="relative flex items-center justify-center"
        style={{ width: scaledSize, height: scaledSize }}
      >
        <svg
          width={scaledSize}
          height={scaledSize}
          viewBox="0 0 100 100"
          style={{ transform: "rotate(-90deg)" }}
        >
          {percentages.map((p, i) => {
            const offset = (cumulative / 100) * circumference;
            cumulative += p;
            return (
              <circle
                key={i}
                cx="50"
                cy="50"
                r={radius}
                fill="transparent"
                stroke={colorSet[i % colorSet.length]}
                strokeWidth="10"
                strokeDasharray={`${(p / 100) * circumference} ${circumference}`}
                strokeDashoffset={-offset}
                style={{
                  cursor: "pointer",
                  transition: "stroke-width 0.3s, opacity 0.3s",
                  opacity: hoveredIndex === null || hoveredIndex === i ? 1 : 0.3,
                  strokeWidth: hoveredIndex === i ? 12 : 10,
                }}
                onMouseEnter={() => setHoveredIndex(i)}
                onMouseLeave={() => setHoveredIndex(null)}
              />
            );
          })}
        </svg>

        {/* Center text changes on hover */}
        <div
          className="absolute flex flex-col items-center justify-center text-center font-semibold select-none"
          style={{
            fontSize: scaledSize / 10,
            lineHeight: 1.2,
          }}
        >
          {hoveredIndex === null ? (
            <>
              <div>Total</div>
              <div>{total}</div>
            </>
          ) : (
            <>
              <div>{labels[hoveredIndex]}</div>
              <div>{values[hoveredIndex]}</div>
              <div style={{ fontSize: scaledSize / 15, color: "#666" }}>
                {percentages[hoveredIndex].toFixed(1)}%
              </div>
            </>
          )}
        </div>
      </div>

      {/* Legend below */}
      <div className="flex flex-wrap justify-center gap-3 mt-3 text-sm">
        {labels.map((label, i) => (
          <div key={i} className="flex items-center gap-2">
            <span
              className="inline-block w-3 h-3 rounded-full"
              style={{ backgroundColor: colorSet[i % colorSet.length] }}
            />
            <span>{label}</span>
            <span className="text-gray-500">
              ({percentages[i].toFixed(1)}%)
            </span>
          </div>
        ))}
      </div>
    </div>
  );
};

export default DonutChart;
