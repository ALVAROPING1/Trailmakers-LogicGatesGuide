---
title: Logic Blocks Guide
author: ALVAROPING1
date: \today
lang: en

toc: true
toc-depth: 3

linkcolor: hyperlinkBlue
urlcolor: hyperlinkBlue

geometry: "top=2.54cm, bottom=2.54cm, left=2.54cm, right=2.54cm"

header-includes: |
    ```{=latex}
    % Fonts
    \parskip=3.5pt

    % Hyperlink color
    \definecolor{hyperlinkBlue}{RGB}{5,99,193}

    % Custom horizontal line width on tables (tabular/aligned)
    \usepackage{booktabs}
    \aboverulesep=0ex
    \belowrulesep=0ex
    \newcommand\HLine[1]{\specialrule{#1}{0pt}{0pt}}

    % Multi page tables
    \usepackage{longtable}

    % Force figure location
    \usepackage{float}
    % Correct hiperlink to figures
    \usepackage{caption}

    % Add table of figures/tables, we can't use pandoc's built-in option because it doesn't remove color like for the table of contents
    \newcommand{\lists}{{\hypersetup{linkcolor=} \listoffigures \listoftables}}

    % Multi line table cells
    \usepackage{makecell}
    \renewcommand\theadfont{\bfseries}

    % Increase table cell height
    \renewcommand{\arraystretch}{1.4}

    % Increase table header height
    \renewcommand\theadgape{\Gape[5pt]}

    % Merge rows/columns in tables
    \usepackage{multirow}

    % Vertically center table cells
    \usepackage{array}

    % Custom list labels
    \newcommand{\LabelItemI}{\labelitemfont \textbullet}
    \newcommand{\LabelItemII}{\labelitemfont \bfseries \textendash}
    \newcommand{\LabelItemIII}{\labelitemfont \rule[0.5ex]{0.6ex}{0.6ex}}
    \newcommand{\LabelItemIV}{\labelitemfont \textasteriskcentered}

    % Increase max nesting depth for lists
    \usepackage{enumitem}

    \setlistdepth{5}

    \setlist[itemize]{leftmargin=2em}
    \setlist[itemize,1]{label=\LabelItemI, leftmargin=2.5em}
    \setlist[itemize,2]{label=\LabelItemII}
    \setlist[itemize,3]{label=\LabelItemIII}
    \setlist[itemize,4]{label=\LabelItemIV}
    \setlist[itemize,5]{label=\LabelItemI}

    \renewlist{itemize}{itemize}{5}

    % Diagrams
    \usepackage{tikz}
    \usetikzlibrary{positioning, fit, calc, arrows.meta, backgrounds, decorations.markings}
    % Node/line/arrow styles
    \tikzset{
        % Basic node
        rectangleNode/.style={rectangle, draw=black, fill=white, thick, minimum height=6.6mm},
        % Node for image annotations
        annotation/.style={rectangleNode, align=left},
        % Node for diagram nodes
        node/.style={rectangleNode, align=center, minimum width=30.4mm},
        % Node for hidden diagram nodes
        hiddenNode/.style={rectangleNode, draw=none, fill=none, minimum width=5mm},
        % Style for lines
        line/.style={-, thick},
        % Style for arrows
        arrow/.style={->, thick},
        % Style for arrows with the tip on the middle
        ->-/.style={line, decoration={markings, mark=at position 0.5 with {\arrow{>}}}, postaction={decorate}},
    }
    % Custom to-paths
    \tikzset{
        % Vertical line through the end point
        *|/.style={/tikz/to path={(\tikztostart -| \tikztotarget) -- (\tikztotarget) \tikztonodes}}
    }

    % Plot graphs
    \usepackage{pgfplots}
    \pgfplotsset{every axis plot/.append style={very thick}}

    % Images location
    \graphicspath{ {img/} }

    \usepackage{mathtools}
    % Ceiling function
    \DeclarePairedDelimiter{\ceil}{\lceil}{\rceil}
    % Absolute value function
    \DeclarePairedDelimiter{\abs}{|}{|}

    % Title command
    \makeatletter
    \renewcommand{\maketitle}{
        \pagenumbering{roman}
        \begin{center}
            \LARGE\underline{\@title}\\
            \vspace{1mm}
            \begin{figure}[h]
                \makebox[\textwidth][c]{
                    \includegraphics[width=\linewidth]{thumbnail}
                }
            \end{figure}
            \raggedleft\large{\@date} \\
        \end{center}
    }
    \makeatother

    % Maximum non-infinity IEEE float value
    \newcommand{\maxFloat}{(2 - 2^{-23}) \cdot 2^{127}}
    \newcommand{\maxFloatApprox}{3.403 \cdot 10^{38}}

    \usepackage{gensymb} % \degree symbol
    ```
---

\lists
\clearpage
\pagenumbering{arabic}

# Introduction

The goal of this document is to explain the logic system in the game [\underline{Trailmakers}](https://store.steampowered.com/app/585420/Trailmakers/). It also aims to act as a comprehensive technical reference manual for all related mechanics and behaviours. This document also contains a section about commonly used logic circuits and how to make them, to aid in the design and understanding of more complex logic circuits.

# Logic Blocks

Logic blocks are a group of blocks that allow to obtain and process information, which in turn can be used to automate tasks or create more complex control schemes for creations, and more generally, perform any finite sequence of steps (known as executing an algorithm).

All logic blocks, with the exception of distance/gravity sensors and number displays, have a display with an arrow pointing away from the center of the block representing the value of their output signal. This arrow is empty when there is no output ($0$ value), and green/red when the output is positive/negative. On gravity sensors, this arrow is replaced with a weight icon, which has the same functionality (although it is white when there is no output). Blocks which can take other signals as inputs (\nameref{logic-gates} and \nameref{math-blocks}), with the exception of number displays, additionally have a second arrow pointing to the center of the block representing the input signals, which works like the output arrow but using the value of the sum of the input signals.

Note: due to a bug, only up to 5 characters can be used on any configurable block value. Even though the UI rounds values 1-2 decimals, the values used are always the values that were typed, with the exception of values in the range $(-0.0001, 0.0001)$ which get rounded to $0$.

## Sensors

Sensors are a group of blocks that measure a physical property, like speed or angle, and create a boolean output based on it.

### Distance Sensor

Distance sensors check for objects within a straight line in front of them and a predefined distance. Objects are detected with a raycast from a point in the detecting face, indicated by a red dot. The detecting face glows white when the sensor creates an output, and stays off otherwise.

Its settings are shown in figure \ref{fig:SensorDistance} and are as follows:

- Range: maximum distance between an object and the sensor for it to be detected, in meters ($1 \text{ block} = 0.25 \text{ m}$)
  - Distance is measured from the surface of the detecting face
- Output scale: multiplier of the output signal created by the block
- Sensor offset: horizontal position from which the detecting face sends the raycast to detect objects
- Output mode: type of output created by the sensor when it is activated (the output is always $0$ otherwise)
  - Trigger: output 1
  - Measurement: output the distance to the detected object in meters
    - Same as "Trigger" when inverted trigger is used
  - Normalized: output $\frac{\text{measurement}}{\text{range}}$
- Trigger: condition used to determine when to send an output
  - Normal: sends an output when it detects an object
  - Inverted: sends an output when it doesn't detect an object

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=4.7cm]{distance_sensor}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (output_on)    at (-1, 16)      {Output (on)};
            \node[annotation, right] (output_off)   at (21, 16)      {Output (off)};
            \node[annotation, left]  (range)        at (-1, 3)       {Range};
            \node[annotation, below] (output_value) at (3.75, -1.5)  {Output scale};
            \node[annotation, below] (offset)       at (9.2, -1.5)   {Sensor offset};
            \node[annotation, below] (mode)         at (14.75, -1.5) {Output mode};
            \node[annotation, right] (trigger)      at (21, 0)       {Invert trigger};

            % Arrows
            \draw[arrow] (output_on.east)         -- (5.7, 16);
            \draw[arrow] (output_off.west)        -- (13.5, 16);
            \draw[arrow] (range.east)             -- (0.15, 3);
            \draw[arrow] (output_value.north) to[*|] (5, 1);
            \draw[arrow] (output_value.north) to[*|] (8.4, 1);
            \draw[arrow] (mode.north)             -- (12.5, 1.7);
            \draw[arrow] (trigger.west)           -- (17.2, 2.1);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Distance Sensor settings}
    \label{fig:SensorDistance}
\end{figure}

### Altitude Sensor

Altitude sensors measure the altitude of the block relative to a predefined frame of reference. They have a display which shows the currently measured altitude rounded to the nearest integer, or "N/A" in build mode.

Its settings are shown in figure \ref{fig:SensorAltitude} and are as follows:

- Altitude: altitude threshold to trigger, in meters above the frame of reference ($1 \text{ block} = 0.25 \text{ m}$)
- Output scale: multiplier of the output signal created by the block
- Output mode: type of output created by the sensor when it is activated (the output is always $0$ otherwise)
  - Trigger: output 1
  - Measurement: output the current altitude in meters
  - Normalized: output $\frac{\text{measurement}}{\text{altitude}}$
- Frame of reference: position of the $0$ altitude point
  - Ignore waves: fixed at the average sea level
  - Relative to waves: at the position of the water surface at the horizontal coordinates of the sensor
  - Outside of high seas and when the wave setting is set to disabled, both options are equivalent
  - On space sector, it's a "MAX" value (equivalent to $2001$ m) while outside an atmosphere and the distance to a point close to the center of the planet while inside of one
- Trigger: condition used to determine when to send an output
  - Normal: sends an output when the altitude is above the configured value
  - Below: sends an output when the altitude is below the configured value

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=4.7cm]{altitude_sensor}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (output_on)       at (-1, 16)     {Output (on)};
            \node[annotation, right] (output_off)      at (21, 16)     {Output (off)};
            \node[annotation, left]  (altitude)        at (-1, 3)      {Altitude};
            \node[annotation, below] (output_value)    at (4.25, -1.5) {Output scale};
            \node[annotation, below] (mode)            at (11.5, -1.5) {Output mode};
            \node[annotation, below] (reference_frame) at (19.5, -1.5) {Frame of reference};
            \node[annotation, right] (trigger)         at (21, 3)      {Trigger below};

            % Arrows
            \draw[arrow] (output_on.east)            -- (6.8, 16);
            \draw[arrow] (output_off.west)           -- (16, 16);
            \draw[arrow] (altitude.east)             -- (0.15, 3);
            \draw[arrow] (output_value.north)    to[*|] (5.9, 1);
            \draw[arrow] (mode.north)                -- (11.5, 1.7);
            \draw[arrow] (reference_frame.north) to[*|] (15.85, 0.4);
            \draw[arrow] (trigger.west)              -- (19.3, 3);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Altitude Sensor settings}
    \label{fig:SensorAltitude}
\end{figure}

### Speed Sensor

Speed sensors measure the speed of the block in a given direction indicated by the arrow on the block. They have a display which shows the currently measured speed as a bar indicator, which is full when the speed is higher than or equal to the trigger speed and empty when the speed is negative or $0$.

Its settings are shown in figure \ref{fig:SensorSpeed} and are as follows:

- Speed: speed threshold to trigger, in km/h or mph depending on the speed unit settings
- Output scale: value of the output signal created by the block
- Output mode: type of output created by the sensor when it is activated (the output is always $0$ otherwise)
  - Trigger: output 1
  - Measurement: output the current speed in m/s
  - Normalized: output $\frac{\text{measurement}}{\text{speed (in m/s)}}$
- Trigger: condition used to determine when to send an output
  - Normal: sends an output when the speed is above the configured value
  - Below: sends an output when the speed is below the configured value

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=5cm]{speed_sensor}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (output_on)       at (-1, 16)     {Output (on)};
            \node[annotation, right] (output_off)      at (21, 16)     {Output (off)};
            \node[annotation, left]  (altitude)        at (-1, 3)      {Altitude};
            \node[annotation, below] (output_value)    at (4.25, -1.5) {Output scale};
            \node[annotation, below] (mode)            at (11.5, -1.5) {Output mode};
            \node[annotation, right] (trigger)         at (21, 0)      {Trigger below};

            % Arrows
            \draw[arrow] (output_on.east)         -- (6.8, 16);
            \draw[arrow] (output_off.west)        -- (16, 16);
            \draw[arrow] (altitude.east)          -- (0.15, 3);
            \draw[arrow] (output_value.north) to[*|] (5.9, 1);
            \draw[arrow] (mode.north)             -- (11.5, 1.7);
            \draw[arrow] (trigger.west)           -- (16.5, 2.1);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Speed Sensor settings}
    \label{fig:SensorSpeed}
\end{figure}

### Gravity Sensor

Gravity sensors measure the gravity strength at the position of the block. They have a display which shows the currently measured gravity as a bar indicator, which is full when the gravity is higher than or equal to the trigger gravity and empty when the gravity is $0$.

Its settings are shown in figure \ref{fig:SensorGravity} and are as follows:

- Threshold: gravity strength threshold to trigger, relative to the normal gravity ($14 \text{m}/\text{s}^2$)
- Output scale: multiplier of the output signal created by the block
- Output mode: type of output created by the sensor when it is activated (the output is always $0$ otherwise)
  - Trigger: output 1
  - Measurement: output the current gravity relative to the normal gravity
  - Normalized: output $\frac{\text{measurement}}{\text{threshold}}$
- Trigger: condition used to determine when to send an output
  - Normal: sends an output when the gravity strength is above the configured value
  - Below: sends an output when the gravity strength is below the configured value

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=4.65cm]{gravity_sensor}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (output_on)    at (-1, 16)     {Output (on)};
            \node[annotation, right] (output_off)   at (21, 16)     {Output (off)};
            \node[annotation, left]  (altitude)     at (-1, 3)      {Altitude};
            \node[annotation, below] (output_value) at (4.25, -1.5) {Output scale};
            \node[annotation, below] (mode)         at (11.5, -1.5) {Output mode};
            \node[annotation, right] (trigger)      at (21, 0)      {Trigger below};

            % Arrows
            \draw[arrow] (output_on.east)         -- (6.8, 16);
            \draw[arrow] (output_off.west)        -- (16, 16);
            \draw[arrow] (altitude.east)          -- (0.15, 3);
            \draw[arrow] (output_value.north) to[*|] (5.9, 1);
            \draw[arrow] (mode.north)             -- (11.5, 1.7);
            \draw[arrow] (trigger.west)           -- (16.5, 2.1);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Gravity Sensor settings}
    \label{fig:SensorGravity}
\end{figure}

### Angle Sensor

Angle sensors measure the angle of the block relative to the direction of highest slope of the plane defined by the square faces of the block. They have a display which shows the currently measured angle, with a blue section representing the activation threshold and an output arrow representing the angle. The arrow will always try to point up no matter the orientation of the block (will point in the direction of highest slope of the plane it is in).

Its settings are shown in figure \ref{fig:SensorAngle} and are as follows:

- Direction: position of the middle point of the activation threshold, in degrees
- Width: size of the activation threshold, in degrees
- Output scale: multiplier of the output signal created by the block
- Output mode: type of output created by the sensor when it is activated (the output is always $0$ otherwise)
  - Trigger: output 1
  - Measurement: output the current signed angle (positive for counterclockwise) from either the center (normal trigger) or the closest edge (trigger outside) of the activation threshold to the output arrow in degrees
  - Normalized: output $\frac{\text{measurement}}{\text{width}/2}$
- Trigger: condition used to determine when to send an output
  - Normal: sends an output when the angle is inside of the activation threshold
  - Outside: sends an output when the angle is outside the activation threshold

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=4.75cm]{angle_sensor}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (output_on)    at (-1, 17)    {Output (on)};
            \node[annotation, right] (output_off)   at (21, 17)    {Output (off)};
            \node[annotation, left]  (direction)    at (-1, 3)     {Direction};
            \node[annotation, below] (width)        at (4.1, -1.5) {Width};
            \node[annotation, below] (output_value) at (8.4, -1.5) {Output scale};
            \node[annotation, below] (mode)         at (14, -1.5)  {Output mode};
            \node[annotation, right] (trigger)      at (21, 0)     {Trigger outside};

            % Arrows
            \draw[arrow] (output_on.east)         -- (7.1, 17);
            \draw[arrow] (output_off.west)        -- (12.4, 17);
            \draw[arrow] (direction.east)         -- (0.15, 3);
            \draw[arrow] (width.north)        to[*|] (5, 1);
            \draw[arrow] (output_value.north)     -- (8.4, 1);
            \draw[arrow] (mode.north)             -- (12.5, 1.7);
            \draw[arrow] (trigger.west)           -- (17.3, 2.1);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Angle Sensor settings}
    \label{fig:SensorAngle}
\end{figure}

### Compass

Compasses measure the angle of the block relative to the closest direction to the north in the plane defined by the square faces of the block. They have a display which shows the currently measured angle, with a red section representing the activation threshold, and an output arrow and cardinal direction letters representing the angle. The arrow will always try to point north no matter the orientation of the block (will point in the direction closest to the north of the plane it is in).

Its settings are shown in figure \ref{fig:SensorCompass} and are as follows:

- Direction: position of the middle point of the activation threshold, in degrees
- Width: size of the activation threshold, in degrees
- Output scale: multiplier of the output signal created by the block
- Output mode: type of output created by the sensor when it is activated (the output is always $0$ otherwise)
  - Trigger: output 1
  - Measurement: output the current signed angle (positive for counterclockwise) from either the center (normal trigger) or the closest edge (trigger outside) of the activation threshold to the output arrow in degrees
  - Normalized: output $\frac{\text{measurement}}{\text{width}/2}$
- Trigger: condition used to determine when to send an output
  - Normal: sends an output when the angle is inside of the activation threshold
  - Outside: sends an output when the angle is outside the activation threshold

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=5cm]{compass}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (output_on)    at (-1, 17.2)  {Output (on)};
            \node[annotation, right] (output_off)   at (21, 17.2)  {Output (off)};
            \node[annotation, left]  (direction)    at (-1, 3)     {Direction};
            \node[annotation, below] (width)        at (4.1, -1.5) {Width};
            \node[annotation, below] (output_value) at (8.4, -1.5) {Output scale};
            \node[annotation, below] (mode)         at (14, -1.5)  {Output mode};
            \node[annotation, right] (trigger)      at (21, 0)     {Trigger outside};

            % Arrows
            \draw[arrow] (output_on.east)         -- (8.1, 17.2);
            \draw[arrow] (output_off.west)        -- (13.5, 17.2);
            \draw[arrow] (direction.east)         -- (0.15, 3);
            \draw[arrow] (width.north)        to[*|] (5, 1);
            \draw[arrow] (output_value.north)     -- (8.4, 1);
            \draw[arrow] (mode.north)             -- (12.5, 1.7);
            \draw[arrow] (trigger.west)           -- (17.3, 2.1);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Compass settings}
    \label{fig:SensorCompass}
\end{figure}

## Logic gates

Logic gates are a group of blocks that take a set of boolean inputs, and create a boolean output based on their values. The conditions used are as follows, inputs will be discussed on \nameref{signals}:

- AND gate: all inputs are on
- OR gate: at least one input is on
- XOR gate: only one input is on
- NOR gate: sum of inputs is $0$

Their settings are shown in figure \ref{fig:LogicGate} and are as follows:

- Keybinds: see \nameref{keybinds}
- Toggle: see \nameref{toggle}
- Timers: see \nameref{timers}
- Output scale: multiplier of the output signal created by the block, explained in \nameref{output-value-calculation}

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=5cm]{logic_gate}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (output_on)     at (-0.75, 16.2) {Output (on)};
            \node[annotation, right] (output_off)    at (20.75, 16.2) {Output (off)};
            \node[annotation, left]  (red_keybind)   at (-0.75, 4.5)  {Red keybind};
            \node[annotation, below] (green_keybind) at (7.4, -1.5)   {Green keybind};
            \node[annotation, below] (toggle)        at (1, -1.5)     {Green/red toggle};
            \node[annotation, below] (timers)        at (12, -1.5)    {Timers};
            \node[annotation, below] (output_value)  at (15.4, -1.5)  {Output scale};

            % Arrows
            \draw[arrow] (output_on.east)      -- (7.4, 16.2);
            \draw[arrow] (output_off.west)     -- (13.2, 16.2);
            \draw[arrow] (red_keybind.east)    -- (0.1, 4.5);
            \draw[arrow] (green_keybind.north) -- (7.4, 3.1);
            \draw[arrow] (toggle.north)        -- (0.5, 1.5);
            \draw[arrow] (toggle.north)        -- (4.9, 2.1);
            \draw[arrow] (timers.north)        -- (12, 0.3);
            \draw[arrow] (output_value.north)  -- (15.4, 1);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Logic Gate settings}
    \label{fig:LogicGate}
\end{figure}

### Output value calculation

These are the steps used by the game to determine the value attached to the output signal created by logic gates. For more information about signals, see \nameref{signals}.

1) The gate checks if its conditions are met. If they aren't, the gate doesn't create an output
2) The gate adds up the values of all of its inputs and clamps the result to the $[-1, 1]$ range
   - Values smaller than $-1$ are replaced with $-1$, and values bigger than $1$ with $1$
3) The gate multiplies the result by its Output scale setting. For NOR gates, their setting replaces the result (which would otherwise always be $0$)
4) The gate sends the result as its output scale

This process can be described with the following formula:
$$\text{output} = \text{output\_value} \cdot \operatorname{boolean\_operation}(\text{inputs}) \cdot \sum{\text{inputs}}$$

\begin{figure}[H]
    \centering
    \includegraphics[width=\linewidth]{output_value_diagram}
    \caption{Output value calculation diagram made by Zoomah}
    \label{fig:OutputValueDiagram}
\end{figure}

#### Example

An AND gate with an output value of $0.5$ has 2 inputs, one of them has an output value of $0.8$ and the other of $0.5$. When at least one of them is off, it doesn't send an output. When both of them are on at the same time, the AND gate is able to send an output. On that case, the output values of the inputs are first added up: $0.8 + 0.5 = 1.3$. Because the sum, $1.3$, is bigger than $1$, the gate replaces it with $1$. Then that value is multiplied by the output value of the gate: $1 \cdot 0.5 = 0.5$. Finally, the AND gate sends an output with the value of that multiplication, $0.5$. On the steam version, if the sum of the inputs or the output value of the gate had been $0$, the resultant value of the multiplication would have also been $0$, in which case the gate wouldn't have sent an output.

## Math blocks

Math blocks are a group of blocks that take a set of numeric inputs and perform some operation to get an output based on their values, either numeric or boolean. They are the only blocks which don't clamp the sum of their inputs to the $[-1, 1]$ range before interpreting it.

### Comparison Logic Gate

Comparison logic gates calculate the boolean value of a predefined comparison and return it as their output, where the left hand side is the sum of all their inputs and the right hand side is a constant. They have a display which shows the currently selected comparison mode. They are also sometimes referred to as comparators.

Its settings are shown in figure \ref{fig:Comparator} and are as follows:

- Keybinds: see \nameref{keybinds}
- Toggle: see \nameref{toggle}
- Timers: see \nameref{timers}
- Threshold: value used for the right hand side of the comparison
- Output value: value of the output signal created by the block, explained in \nameref{signals}
- Comparison mode: comparison operation to perform. Possible values are "less than", "less than or equal", "greater than", "greater than or equal", "equal", and "not equal"
  - When the mode is set to "greater than or equal", the total input is rounded to 3 decimal places before performing the comparison, using the nearest mode and rounding numbers with a fractional part of $0.5$ up
  - For the "equal" and "not equal" modes, numbers are compared with a tolerance of $\varepsilon = 0.0005$ (the numbers are equal if their difference is $< \varepsilon$)
- Clamp input: whether the result of the sum of the inputs should be clamped to the $[-1, 1]$ range or not

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=4.5cm]{comparison_logic_gate}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (output_on)     at (-0.5, 16)    {Output (on)};
            \node[annotation, right] (output_off)    at (20.5, 16)    {Output (off)};
            \node[annotation, left]  (red_keybind)   at (-0.5, 4.5)   {Red keybind};
            \node[annotation, below] (green_keybind) at (5.25, -1.5)  {Green keybind};
            \node[annotation, below] (toggle)        at (1, -1.5)     {Green/red toggle};
            \node[annotation, below] (timers)        at (8.5, -1.5)   {Timers};
            \node[annotation, below] (threshold)     at (11.25, -1.5) {Threshold};
            \node[annotation, below] (mode)          at (15.25, -1.5) {Comparison mode};
            \node[annotation, right] (clamp)         at (20.5, 0)     {Clamp input};
            \node[annotation, right] (output_value)  at (20.5, 9.5)   {Output value};

            % Arrows
            \draw[arrow] (output_on.east)          -- (8.55, 16);
            \draw[arrow] (output_off.west)         -- (12.7, 16);
            \draw[arrow] (red_keybind.east)        -- (0.1, 4.5);
            \draw[arrow] (green_keybind.north)     -- (5.25, 3.1);
            \draw[arrow] (toggle.north)            -- (0.35, 1.5);
            \draw[arrow] (toggle.north)            -- (3.7, 2.1);
            \draw[arrow] (timers.north)        to[*|] (9, 0.3);
            \draw[arrow] (threshold.north)         -- (11.25, 1);
            \draw[arrow] (mode.north)              -- (15.25, 1.7);
            \draw[arrow] (clamp.west)              -- (18.45, 2.1);
            \draw[arrow] (output_value.west)       -- (14.05, 5.5);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Comparator Logic Gate settings}
    \label{fig:Comparator}
\end{figure}

### Accumulator

Accumulators store and output a numeric value, and allow to increment/decrement it between some bounds. They are always initialized to the value closest to $0$ within the defined bounds. They have a display which shows the currently stored value as a bar indicator, which is full when the value is the maximum and empty when it is the minimum. Additionally, the bar indicator is white when the value is 0, and green/red when it is positive/negative.

Its settings are shown in figure \ref{fig:Accumulator} and are as follows:

- Keybinds: see \nameref{keybinds}
- Toggle: see \nameref{toggle}
- Timers: see \nameref{timers}
- Value bounds: minimum/maximum value that can be stored, the stored value will be clamped to the range $[\min(\text{minimum}, \text{maximum}), \enspace \max(\text{minimum}, \text{maximum})]$
- Scale: rate of change of the stored value, used to scale the value of the input
- Use steps: whether to change the stored value continuously (in which case the scale is change per second, achieved by using $1/60$th the scale on each frame) or only once per input activation (on the rising edge of the signal)

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=4.5cm]{accumulator}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (output_on)     at (-0.5, 16)   {Output (on)};
            \node[annotation, right] (output_off)    at (20.5, 16)   {Output (off)};
            \node[annotation, left]  (red_keybind)   at (-0.5, 4.5)  {Red keybind};
            \node[annotation, below] (green_keybind) at (5.5, -1.5)  {Green keybind};
            \node[annotation, below] (toggle)        at (1, -1.5)    {Green/red toggle};
            \node[annotation, below] (timers)        at (9.5, -1.5)  {Timers};
            \node[annotation, below] (bounds)        at (13.2, -1.5) {Value bounds};
            \node[annotation, below] (scale)         at (16.4, -1.5) {Scale};
            \node[annotation, right] (steps)         at (20.5, 0)    {Use steps};

            % Arrows
            \draw[arrow] (output_on.east)          -- (9.2, 16);
            \draw[arrow] (output_off.west)         -- (13.75, 16);
            \draw[arrow] (red_keybind.east)        -- (0.1, 4.5);
            \draw[arrow] (green_keybind.north)     -- (5.5, 3.1);
            \draw[arrow] (toggle.north)            -- (0.35, 1.5);
            \draw[arrow] (toggle.north)            -- (3.9, 2.1);
            \draw[arrow] (timers.north)        to[*|] (10, 0.3);
            \draw[arrow] (bounds.north)            -- (12.2, 1);
            \draw[arrow] (bounds.north)            -- (14.2, 1);
            \draw[arrow] (scale.north)             -- (16.4, 1);
            \draw[arrow] (steps.west)              -- (18.3, 2.1);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Accumulator settings}
    \label{fig:Accumulator}
\end{figure}

### Randomizer

Randomizers generate and output random values.

Its settings are shown in figure \ref{fig:Randomizer} and are as follows:

- Keybinds: see \nameref{keybinds}
- Toggle: see \nameref{toggle}
- Timers: see \nameref{timers}
- Value bounds: minimum/maximum value that can be generated, the generated values will be in the range $[\min(\text{minimum}, \text{maximum}), \enspace \max(\text{minimum}, \text{maximum})]$
- Random mode: mode in which the values are generated
  - Output on input: outputs a random value generated on each frame when it is activated, and $0$ otherwise
  - Change on input: outputs a random value generated on each frame when it is activated, and the last generated value (initialized to $0$) otherwise
  - Input defines range: outputs a random value generated on each frame in the intersection between the value bounds and the range $[\min(\text{inputs}), \max(\text{inputs})]$ when the block is activated, and the last generated value (initialized to a random value within the value bounds) otherwise
    - If the intersection is empty, the value bounds range is used instead if any of the inputs isn't 0, otherwise the output is always 0
  - -Change, +Output: generates and stores a random value on each frame when the block is activated with a negative input, and outputs the last generated value (initialized to a random value within the value bounds) when it is activated with a positive input

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=4.5cm]{randomizer}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (output_on)     at (-0.5, 16)    {Output (on)};
            \node[annotation, right] (output_off)    at (20.5, 16)    {Output (off)};
            \node[annotation, left]  (red_keybind)   at (-0.5, 4.5)   {Red keybind};
            \node[annotation, below] (green_keybind) at (5.5, -1.5)   {Green keybind};
            \node[annotation, below] (toggle)        at (1, -1.5)     {Green/red toggle};
            \node[annotation, below] (timers)        at (9.25, -1.5)  {Timers};
            \node[annotation, below] (bounds)        at (12.25, -1.5) {Value bounds};
            \node[annotation, below] (mode)          at (16.0, -1.5)  {Random mode};

            % Arrows
            \draw[arrow] (output_on.east)      -- (8.55, 16);
            \draw[arrow] (output_off.west)     -- (12.75, 16);
            \draw[arrow] (red_keybind.east)    -- (0.1, 4.5);
            \draw[arrow] (green_keybind.north) -- (5.5, 3.1);
            \draw[arrow] (toggle.north)        -- (0.35, 1.5);
            \draw[arrow] (toggle.north)        -- (3.65, 2.1);
            \draw[arrow] (timers.north)        -- (9.25, 0.3);
            \draw[arrow] (bounds.north)        -- (11.25, 1);
            \draw[arrow] (bounds.north)        -- (13.25, 1);
            \draw[arrow] (mode.north)          -- (16.0, 1.7);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Randomizer settings}
    \label{fig:Randomizer}
\end{figure}

### Number Display

Number displays output the sum of their inputs, similar to how OR logic gates work (although without clamping the sum to the $[-1, 1]$ range), and display it with an optional rounding. They display "N/A" in build mode.

Its settings are shown in figure \ref{fig:NumberDisplay} and are as follows:

- Keybinds: see \nameref{keybinds}
- Toggle: see \nameref{toggle}
- Timers: see \nameref{timers}
- Rounding: rounding mode applied to the sum of the inputs, always done to an integer when enabled. Possible values are "disabled", "nearest", "floor" (closest smaller integer), and "ceil" (closest bigger integer)
  - When rounding is disabled, numbers are rounded to $2$ decimals using the nearest mode, rounding numbers ending in $0.005$ away from $0$
  - For the "nearest" mode, numbers with a fractional part of $0.5$ are rounded to the closest even integer
  - If the number is outside of the $[-1000, 1000]$ range, it's always displayed in scientific notation with $1$ decimal of precision

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=5cm]{number_display}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (output_on)     at (-0.75, 16.25) {Output (on)};
            \node[annotation, right] (output_off)    at (20.75, 16.25) {Output (off)};
            \node[annotation, left]  (red_keybind)   at (-0.75, 4.5)   {Red keybind};
            \node[annotation, below] (green_keybind) at (7.0, -1.5)    {Green keybind};
            \node[annotation, below] (toggle)        at (1, -1.5)      {Green/red toggle};
            \node[annotation, below] (timers)        at (11.5, -1.5)   {Timers};
            \node[annotation, below] (rounding)      at (14.5, -1.5)   {Rounding};

            % Arrows
            \draw[arrow] (output_on.east)      -- (6.85, 16.25);
            \draw[arrow] (output_off.west)     -- (12.15, 16.25);
            \draw[arrow] (red_keybind.east)    -- (0.1, 4.5);
            \draw[arrow] (green_keybind.north) -- (7.0, 3.1);
            \draw[arrow] (toggle.north)        -- (0.5, 1.5);
            \draw[arrow] (toggle.north)        -- (4.5, 2.0);
            \draw[arrow] (timers.north)        -- (11.5, 0.3);
            \draw[arrow] (rounding.north)      -- (14.5, 1.7);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Number Display settings}
    \label{fig:NumberDisplay}
\end{figure}

### Arithmetics Logic Block

Arithmetics logic blocks perform an arithmetic binary operation with a constant and the sum of their inputs, and output the result. The constant is the first operand while the sum of the inputs is the second one. They have a display which shows the currently selected operation.

Its settings are shown in figure \ref{fig:ArithmeticsBlock} and are as follows:

- Keybinds: see \nameref{keybinds}
- Toggle: see \nameref{toggle}
- Timers: see \nameref{timers}
- Constant: constant value to use as the first operand
- Operation: binary operation to perform. Possible values are addition, subtraction, multiplication, division, modulo, power ($x^\text{constant}$), and exponentiation ($\text{constant}^x$)
  - Attempting to perform a division by $0$, $\text{constant}^x$ with $\text{constant} < 0$, or $x^\text{constant}$ with $\abs{\text{constant}} < 1$ results in an output of $0$
  - Attempting to perform $0^0$ results in $1$ when the operation is power and $0$ when the operation is exponentiation
  - When there are no inputs, the operation is performed using an input of $0$

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=4.5cm]{arithmetic_logic_block}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (output_on)     at (-0.75, 16)   {Output (on)};
            \node[annotation, right] (output_off)    at (20.75, 16)   {Output (off)};
            \node[annotation, left]  (red_keybind)   at (-0.75, 4.5)  {Red keybind};
            \node[annotation, below] (green_keybind) at (6, -1.5)     {Green keybind};
            \node[annotation, below] (toggle)        at (1, -1.5)     {Green/red toggle};
            \node[annotation, below] (timers)        at (9.5, -1.5)   {Timers};
            \node[annotation, below] (constant)      at (12.5, -1.5)  {Constant};
            \node[annotation, below] (operation)     at (15.75, -1.5) {Operation};

            % Arrows
            \draw[arrow] (output_on.east)          -- (9.4, 16);
            \draw[arrow] (output_off.west)         -- (14.1, 16);
            \draw[arrow] (red_keybind.east)        -- (0.1, 4.5);
            \draw[arrow] (green_keybind.north)     -- (6, 3.1);
            \draw[arrow] (toggle.north)            -- (0.35, 1.5);
            \draw[arrow] (toggle.north)            -- (3.95, 2.1);
            \draw[arrow] (timers.north)        to[*|] (10, 0.3);
            \draw[arrow] (constant.north)          -- (12.5, 1);
            \draw[arrow] (operation.north)     to[*|] (15.5, 1.7);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Arithmetics Logic Block settings}
    \label{fig:ArithmeticsBlock}
\end{figure}

### Functions Logic Block

Functions logic blocks perform a unary operation with the sum of their inputs and output the result. They have a display which shows the currently selected operation.

Its settings are shown in figure \ref{fig:FunctionsBlock} and are as follows:

- Keybinds: see \nameref{keybinds}
- Toggle: see \nameref{toggle}
- Timers: see \nameref{timers}
- Function: unary operation to perform. Possible values are absolute value, sign, square root, sine, cosine, tangent, arcsine, arccosine, arctangent, floor, ceiling, and rounding
  - Attempting to perform an undefined operation (square root of a negative number, or arcsine/arccosine of a number outside of the $[-1, 1]$ range) results in an output of $0$
  - Trigonometric functions use degrees as the angle unit
  - Inverse trigonometric functions output the value in their [\underline{principal branch}](https://en.wikipedia.org/wiki/Principal_branch)
    - For arcsine: $-90\degree \leq \arcsin(x) \leq 90\degree$
    - For arccosine: $0\degree \leq \arccos(x) \leq 180\degree$
    - For arctangent: $-90\degree < \arcsin(x) < 90\degree$
  - For the rounding function, numbers with a fractional part of $0.5$ are rounded to the closest even integer

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=5cm]{functions_block}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (output_on)     at (-0.75, 16)  {Output (on)};
            \node[annotation, right] (output_off)    at (20.75, 16)  {Output (off)};
            \node[annotation, left]  (red_keybind)   at (-0.75, 4.5) {Red keybind};
            \node[annotation, below] (green_keybind) at (6.5, -1.5)  {Green keybind};
            \node[annotation, below] (toggle)        at (1, -1.5)    {Green/red toggle};
            \node[annotation, below] (timers)        at (11.3, -1.5) {Timers};
            \node[annotation, below] (function)      at (14.5, -1.5) {Function};

            % Arrows
            \draw[arrow] (output_on.east)      -- (7.9, 16);
            \draw[arrow] (output_off.west)     -- (13.1, 16);
            \draw[arrow] (red_keybind.east)    -- (0.1, 4.5);
            \draw[arrow] (green_keybind.north) -- (6.5, 3.1);
            \draw[arrow] (toggle.north)        -- (0.35, 1.5);
            \draw[arrow] (toggle.north)        -- (4.5, 2.1);
            \draw[arrow] (timers.north)        -- (11.3, 0.3);
            \draw[arrow] (function.north)      -- (14.5, 1.7);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Functions Logic Block settings}
    \label{fig:FunctionsBlock}
\end{figure}

### Aggregate Logic Block

Aggregate logic blocks perform a (variadic) aggregation operation with their inputs and output the result. They have a display which shows the currently selected operation. Note: due to a bug, when the function isn't sum, the output is updated instantly without the normal signal propagation delay.

Its settings are shown in figure \ref{fig:AggregateBlock} and are as follows:

- Keybinds: see \nameref{keybinds}
- Toggle: see \nameref{toggle}
- Timers: see \nameref{timers}
- Aggregate function: aggregate operation to perform. Possible values are sum, product, minimum, and maximum

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=5cm]{aggregate_block}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (output_on)     at (-0.75, 16)  {Output (on)};
            \node[annotation, right] (output_off)    at (20.75, 16)  {Output (off)};
            \node[annotation, left]  (red_keybind)   at (-0.75, 4.5) {Red keybind};
            \node[annotation, below] (green_keybind) at (6.5, -1.5)  {Green keybind};
            \node[annotation, below] (toggle)        at (1, -1.5)    {Green/red toggle};
            \node[annotation, below] (timers)        at (11, -1.5)   {Timers};
            \node[annotation, below] (function)      at (15, -1.5)   {Aggregate function};

            % Arrows
            \draw[arrow] (output_on.east)      -- (7.9, 16);
            \draw[arrow] (output_off.west)     -- (13.1, 16);
            \draw[arrow] (red_keybind.east)    -- (0.1, 4.5);
            \draw[arrow] (green_keybind.north) -- (6.5, 3.1);
            \draw[arrow] (toggle.north)        -- (0.35, 1.5);
            \draw[arrow] (toggle.north)        -- (4.4, 2.1);
            \draw[arrow] (timers.north)        -- (11, 0.3);
            \draw[arrow] (function.north)      -- (15, 1.7);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Aggregate Logic Block settings}
    \label{fig:AggregateBlock}
\end{figure}

### Hue Light Panel

Hue light panels are lights with configurable color determined from their input value.

Its settings are shown in figure \ref{fig:HueLightPanel} and are as follows:

- Keybinds: see \nameref{keybinds}
- Toggle: see \nameref{toggle}
- Timers: see \nameref{timers}
- Saturation: saturation of the color of the light. Ignored when the input value is negative
- Brightness: brightness of the color of the light

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        % Image in a node
        \node[anchor=south west, inner sep=0] (image) at (0,0) {\includegraphics[height=5cm]{hue_light_panel}};
        % Use the image as the bounding box of the tikzpicture for centering
        \useasboundingbox (image.south east) rectangle (image.north west);

        % Create scope with normalized axes
        \begin{scope}[
            x={($0.05*(image.south east)$)},
            y={($0.05*(image.north west)$)}
        ]
            % Draw grid
            %\draw[lightgray,step=1] (image.south west) grid (image.north east);

            % Draw axes labels
            %\foreach \x in {0,1,...,20} {\node [below] at (\x,0) {\tiny \x};}
            %\foreach \y in {0,1,...,20} {\node [left]  at (0,\y) {\tiny \y};}

            % Nodes
            \node[annotation, left]  (green_keybind) at (-1, 4.5)     {Green keybind};
            \node[annotation, below] (toggle)        at (0.5, -1.5)   {Toggle};
            \node[annotation, below] (timers)        at (8.25, -1.5)  {Timers};
            \node[annotation, below] (saturation)    at (11.75, -1.5) {Saturation};
            \node[annotation, right] (brightness)    at (21.0, 3)     {Brightness};

            % Arrows
            \draw[arrow] (green_keybind.east) -- (0.1, 4.5);
            \draw[arrow] (toggle.north)       -- (0.5, 1.5);
            \draw[arrow] (timers.north)       -- (8.25, 0.3);
            \draw[arrow] (saturation.north)   -- (11.75, 1);
            \draw[arrow] (brightness.west)    -- (16.15, 3);
        \end{scope}
    \end{tikzpicture}
    \vspace{1cm}
    \caption{Hue Light Panel settings}
    \label{fig:HueLightPanel}
\end{figure}

#### Color Calculation

The color displayed as a function of their input value can be determined with the following formula, where $S$ and $B$ are the saturation and brightness settings respectively. Figure \ref{fig:HueLightPanelColor} shows an example of the resulting colors for inputs in the range $[-1, 1]$ with $\text{saturation} = \text{brightness} = 1$. Inputs smaller than $-1$ result in the $[-1, 0]$ range being repeated, while inputs greater than $1$ result in the $[0, 1]$ range being repeated.

$$\operatorname{color}(x) = \begin{cases}
    \operatorname{HSV}(360(x \bmod{1}), S, B)            & (x > 0) \\
    \text{Off (Black)}                                   & (x = 0) \\
    \operatorname{greyscale}((1 - (x \bmod{1})) \cdot B) & (x < 0) \\
\end{cases}$$

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        \pgfmathsetlengthmacro\MajorTickLength{
          \pgfkeysvalueof{/pgfplots/major tick length} * 2
        }
        \pgfmathsetlengthmacro\MinorTickLength{
          \pgfkeysvalueof{/pgfplots/minor tick length} * 2
        }
        \begin{axis}[
            height=2.5cm,
            width=\linewidth,
            axis on top=true,
            axis y line=none,
            xtick distance=1/6,
            every x tick/.style={thick},
            tick align=center,
            major tick length=\MajorTickLength,
            minor tick length=\MinorTickLength,
            minor tick num=1,
            xmin=-1, xmax=1,
            ymin=-0.1, ymax=0.1,
            samples=500,
            xlabel={Input value},
        ]
            \addplot[
                mesh,
                line width=1cm,
                domain=0:1,
                variable=\u,
                mesh/color input=explicit mathparse,
                point meta={symbolic={Hsb=deg(u * 2 * pi),1,1}}
            ] ({u},0);
            \addplot[
                mesh,
                line width=1cm,
                domain=-1:0,
                variable=\u,
                mesh/color input=explicit mathparse,
                point meta={symbolic={Hsb=0,0,-u}}
            ] ({u},0);
        \end{axis}
    \end{tikzpicture}
    \caption{Hue light panel color with $\text{saturation} = \text{brightness} = 1$ for inputs in the range $[-1, 1]$}
    \label{fig:HueLightPanelColor}
\end{figure}

\clearpage

# Common block settings

These are settings shared by all blocks in the game that can be activated with input signals.

## Keybinds

Keybinds are the most common input to activate a block, and all blocks that can be activated have either a single keybind (green) or two keybinds (green and red).

- All keybinds on a single block act as a single input for each seat
  - An AND gate with a green and a red keybind configured will send an output when just pressing one of the 2 keybinds, but will require someone in each seat that has control over it pressing the keybind to send an output
  - If no keybinds are configured, the input isn't taken into account. This only makes a difference in AND gates, which would otherwise be impossible to trigger without keybinds
- The green keybind has a positive value while the red keybind has a negative one. For more information about input values, see \nameref{signals}
  - For input methods that don't support analog inputs (keyboards and normal buttons on controllers), the value is always $1$
  - For input methods that support analog inputs (controller joysticks and triggers), the value is the one given by the input method normalized to the range $[0, 1]$

## Toggle

Toggle allows to make inputs alternate the activation state of a block between on and off without requiring a continuous input signal to remain active.

- There is a toggle setting associated with positive inputs (below the green keybind) and another with negative ones (below the red keybind)
- Toggles **inputs**
  - When the sum of the inputs goes from $0$ to a different value, multiple things happen depending on the new value:
    1) If the opposite sign of the input is toggled on, it is toggled off instantly
    2) If the sign of the input has toggle enabled, it is toggled: if it was off it turns on, and if it was on it turns off (which will happen on the falling edge of the input). Otherwise, the gate is enabled normally
  - To toggle the output instead of the inputs, make the signal go through another gate with the toggle

## Timers

Timers are a group of settings that allow to automate the activation/deactivation of a block after a set amount of time.

- Timers apply to the inputs of the block and are shared by all of them, and start as soon as the block receives a **single input**. For logic blocks, this still applies even if the block doesn't meet its conditions to send an output
- The number will be rounded to have only 2 decimal places when shown on the menu, but the number which will be used is the one written rounded to 8 decimal places. Due to a bug, only up to 5 characters can be written, so depending on the exact value the number of decimals which can be used will vary
- All values are specified in seconds
- Delay: amount of time between the block receives an input and the block activates
  - Note: each logic gate has an extra delay of $1/60 s$ due to the state of all the logic gates being updated once per physics' frame at the same time
- Duration (previously active time): amount of time before the block automatically deactivates after it has been activated
  - A value of $0$ indicates that it will never deactivate automatically
  - Shortest pulse length is $1$ frame ($1/60 s$), values smaller than this won't activate the block
- Pause (previously inactive time): amount of time before the block reactivates and the duration timer is restarted, after the duration timer expires
  - A value of $0$ indicates that the block will never reactivate automatically
  - It's ignored if the duration timer is $0$
- During the delay/pause timers, the block is deactivated by overriding the input value with $0$
- The order of the timers is as follows: delay $\rightarrow$ duration $\rightarrow$ pause $\rightarrow$ back to duration (if pause is $0$ it ends after the duration ends)
- In the case of the delay and duration timers, even though their values are expressed in seconds, the game handles them as a number of frames, which can be calculated with $\text{seconds} \cdot 60$
  - If this number is not an integer, it will be rounded down to the nearest integer
  - If this number is an integer, it will randomly either be kept as it is or be subtracted one frame depending on the exact value used, so it's recommended to add $0.01$ to the original number to make sure it always stays in the correct number of frames
  - Pause timers aren't subject to this, and the exact time in seconds is used for them

\clearpage

# Signals

Signals are the method used to communicate different logic blocks between eachother and other blocks. All block inputs, both from logic blocks and keybinds, are represented with signals.

- Input/output value: value attached to each signal, usually in the range $[-1, 1]$.
  - \nameref{math-blocks} are the only blocks which don't clamp the sum of their inputs to the $[-1, 1]$ range, instead they use the range $[-\maxFloat, \maxFloat] \approx [-\maxFloatApprox, \maxFloatApprox]$
  - They are represented with a standard [\underline{IEEE 754 single-precision floating-point number}](https://en.wikipedia.org/wiki/Single-precision_floating-point_format)
- Truthness value: value that determines if a signal is on or off
  - A signal is on if its associated value is not $0$. Additionally, for blocks with multiple inputs, the sum of their inputs must also be non-$0$ in order for them to be activated

When a block receives a set of inputs, it determines how it is activated based on the value of their sum. Blocks with a single configurable keybind, except the gyro stabilizer and hue light panels, additionally use the absolute value before interpreting it, which makes both signs equivalent. The resulting value represents the percentage of power that whatever it activates will use, applied to the value set in its settings as a multiplier (if applicable). Values modified for each block are in table \ref{table:InputValueBlocks}. Some important notes:

- For hinges/wings the rotation speed depends on the max angle set in their settings and not on the angle achieved with the input value, resulting in faster speeds with fractional input values for the same final angle
- Due to a bug, fractional inputs in hinges/wings result in angles way lower than they should be. See appendix \nameref{InputValueMultiplier} for more information
- Due to a bug, close to 0 inputs in servos with hold position also result in lower speeds as well. See appendix \nameref{InputValueSpeed} for more information
- For rotating servos, an angle of "infinity" actually represents $360$ degrees
- For the gyro stabilizer, it only works with disabled by default, and negative values make it stabilize in the opposite direction
\begin{table}[H]
    \centering
    \begin{tabular}{p{6cm}p{7.1cm}}
        \toprule
        Block & Value modified \\
        \midrule
        Engines & Max speed and acceleration (torque) \\
        Thrusters, gimbals, propellers, boat engines, and quantum rudders & Power (thrust) \\
        Rotating servos, hinges, and wings with control surfaces (without hold position) & Angle \\
        Spinning servos, helicopter engines, pistons, gyros, and gyro stabilizers & Speed \\
        Tone generators & Volume \\
        Logic gates and math blocks & Output value (see \nameref{logic-gates} and \nameref{math-blocks}) \\
        Hue light panels & Color (see \nameref{hue-light-panel}) \\
        Other & None \\
        \bottomrule
    \end{tabular}
    \caption{Value modified by the input value for each block}
    \label{table:InputValueBlocks}
\end{table}

\clearpage

# Useful Circuits

This section contains commonly used logic circuits and how to make them, to aid in the design and understanding of more complex logic circuits.

## Pulse generator/Rising edge detector

- Sends a pulse of a specific length (usually a single frame which is $1/60 s$) when the input goes from off to on
- Made by setting the duration to the length of the pulse on an OR gate

## Falling edge detector

- Sends a pulse of a specific length when the input goes from on to off
- Made by connecting a NOT gate to a pulse generator

## Latch (T-FlipFlop)

- Stores a single bit of information
- Made by activating toggle on an OR gate, requires the input to be a pulse to be easily controlled by other gates (use a pulse generator for this)

## Counter

- Stores the value of a variable with $n$ possible (discrete) values
- Depending on how it's made, it can be 1 or 2 way and have cycle or not
  - 1-way: the value can only be increased
  - 2-way: the value can be both increased and decreased
  - Cycle: determines if trying to increase/decrease the value past its maximum/minimum will result in it cycling back to the smallest/biggest value or staying at the maximum/minimum value
  - Base designs described are 1-way without cycle, with the modifications needed to implement 2-way or cycle being explained afterwards
- The complexity of a design is the amount of logic gates used by it without counting the ones used to create a startup pulse or always on sensors (those can be reused)
- There are 3 ways of doing it: general circuit (base $N$), decimal (base $10$) and binary (base $2$). Which one is least complex depends on the situation
  - The general circuit and decimal methods can be implemented using logic gates or accumulators. Generally accumulator-based counters are less complex, but logic gate based designs will still be described as they can be less complex under some circumstances (like needing a counter which only changes its value when a function of its current value is true)

### General Circuit (Base $N$)

#### Logic Gates Implementation

- $n = \text{amount of cells}$
- The output is encoded as the position of the active gate in the row of output gates (with the one from the first cell being the minimum value and the one from the last cell being the maximum value)
- Diagram of the circuit:
\begin{figure}[H]
    \makebox[\textwidth][c]{
    \begin{tikzpicture}
        % Nodes

        % Cell 1
        \node[node]       (OR_1)                          {Toggled OR gate\\with 1 frame\\delay (output)};
        \node[node]       (AND_1)   [below = 5mm of OR_1] {AND gate};
        \coordinate[above=16pt of OR_1]   (cell_1);

        % Hidden cell 1
        \node[hiddenNode] (OR_h1)   [right = of OR_1]     {};
        \node[hiddenNode] (AND_h1)  [right = of AND_1]    {};

        % Cell k
        \node[node]       (OR_k)    [right = of OR_h1]    {Toggled OR gate\\with 1 frame\\delay (output)};
        \node[node]       (AND_k)   [right = of AND_h1]   {AND gate};
        \coordinate[above=16pt of OR_k]   (cell_k);

        % Cell k+1
        \node[node]       (OR_k+1)  [right = of OR_k]     {Toggled OR gate\\with 1 frame\\delay (output)};
        \node[node]       (AND_k+1) [right = of AND_k]    {AND gate};
        \coordinate[above=16pt of OR_k+1] (cell_k+1);

        % Hidden cell 2
        \node[hiddenNode] (OR_h2)   [right = of OR_k+1]   {};
        \node[hiddenNode] (AND_h2)  [right = of AND_k+1]  {};

        % Cell n
        \node[node]       (OR_n)    [right = of OR_h2]    {Toggled OR gate\\with 1 frame\\delay (output)};
        \node[hiddenNode] (AND_n)   [right = of AND_h2]   {};
        \coordinate[above=16pt of OR_n]   (cell_n);

        % Input gate
        \node[node] (input_pulse) at ($(AND_k)!0.5!(AND_k+1) + (0, -2.25)$) {1 frame pulse\\generator (input)};

        % Cell bounding boxes
        \begin{scope}[on background layer]
            \node[node,       fit=(OR_1)(AND_1)(cell_1),       label={[anchor=north, yshift=-2.5pt]Cell $1$}] {};
            \node[hiddenNode, fit=(OR_h1)(AND_h1)]             {$\cdots$};
            \node[node,       fit=(OR_k)(AND_k)(cell_k),       label={[anchor=north, yshift=-2.5pt]Cell $k$}] {};
            \node[node,       fit=(OR_k+1)(AND_k+1)(cell_k+1), label={[anchor=north, yshift=-2.5pt]Cell $k+1$}] {};
            \node[hiddenNode, fit=(OR_h2)(AND_h2)]             {$\cdots$};
            \node[node,       fit=(OR_n)(AND_n)(cell_n),       label={[anchor=north, yshift=-2.5pt]Cell $n$}] {};
        \end{scope}

        % Arrows

        % Cell 1
        \draw[arrow] (AND_1.east)                   -- ($(AND_1.east)!0.5!(AND_h1.west)$)   |- (OR_h1.west);
        \draw[arrow] ($(OR_1.south) + (-2mm, 0)$)   -- ($(AND_1.north) + (-2mm, 0)$);
        \draw[arrow] ($(AND_1.north) + (2mm, 0)$)   -- ($(OR_1.south) + (2mm, 0)$);

        % Hidden cell 1
        \draw[arrow] (AND_h1.east)                  -- ($(AND_h1.east)!0.5!(AND_k.west)$)   |- (OR_k.west);

        % Cell k
        \draw[arrow] (AND_k.east)                   -- ($(AND_k.east)!0.5!(AND_k+1.west)$)  |- (OR_k+1.west);
        \draw[arrow] ($(OR_k.south) + (-2mm, 0)$)   -- ($(AND_k.north) + (-2mm, 0)$);
        \draw[arrow] ($(AND_k.north) + (2mm, 0)$)   -- ($(OR_k.south) + (2mm, 0)$);

        % Cell k+1
        \draw[arrow] (AND_k+1.east)                 -- ($(AND_k+1.east)!0.5!(AND_h2.west)$) |- (OR_h2.west);
        \draw[arrow] ($(OR_k+1.south) + (-2mm, 0)$) -- ($(AND_k+1.north) + (-2mm, 0)$);
        \draw[arrow] ($(AND_k+1.north) + (2mm, 0)$) -- ($(OR_k+1.south) + (2mm, 0)$);

        % Hidden cell 2
        \draw[arrow] (AND_h2.east)                  -- ($(AND_h2.east)!0.5!(AND_n.west)$)   |- (OR_n.west);

        % Input gate
        \coordinate  (input) at ($(input_pulse.north) + (0, 0.5)$);
        \draw[line]  (input_pulse.north)            -- (input);
        \draw[arrow] (input)                        -| (AND_1.south);
        \draw[arrow] (input)                        -| (AND_h2.south);
        \draw[arrow] (input -| AND_h1)              -- (AND_h1.south);
        \draw[arrow] (input -| AND_k)               -- (AND_k.south);
        \draw[arrow] (input -| AND_k+1)             -- (AND_k+1.south);
    \end{tikzpicture}
    }
    \caption{Diagram of the general method counter (logic gates implementation)}
    \label{fig:CounterGeneral}
\end{figure}
- To add cycle, add an AND gate to the last cell configured in the same way as the others and using the first cell as its next cell
- To make it 2-way, add a new AND gate to each cell configured in the same way as the other one but going in the opposite direction and using a different pulse generator as input
- Requires a startup pulse to one of the toggled OR gates to work (achieved with a 1 frame pulse generator)
- Complexity
  - 1-way: $2n$
  - 1-way+cycle: $2n + 1$
  - 2-way: $3n$
  - 2-way+cycle: $3n + 1$
- Takes 3 frames to update
- Example blueprints: [\underline{1-way}](https://steamcommunity.com/sharedfiles/filedetails/?id=2134486907), [\underline{1-way+cycle}](https://steamcommunity.com/sharedfiles/filedetails/?id=2134487841), [\underline{2-way}](https://steamcommunity.com/sharedfiles/filedetails/?id=2075055361) and [\underline{2-way+cycle}](https://steamcommunity.com/sharedfiles/filedetails/?id=2134489564)

#### Accumulators Implementation

- $n = \text{amount of comparators}$
- The output is encoded as the active comparator
- Made by choosing $n$ equally spaced numbers, configuring an accumulator to use steps and setting its minimum, maximum, and scale settings to go through those numbers as the value is changed, and connecting the accumulator to $n$ comparators set to equality with each of the chosen numbers
  - The minimum/maximum must be the minimum/maximum chosen numbers, and the scale their spacing. Additionally, in order for the comparators to work, $\text{spacing} \geq 0.001$ must hold
  - If $\text{minimum} \leq 0 \leq \text{maximum}$, $0$ should be within the chosen numbers. Otherwise, the counter needs to be pushed to its minimum/maximum value to initialize it
  - For simplicity, the integers from $0$ to $n-1$ are usually used by setting the minimum/maximum values to $0$/$n-1$ respectively and the scale to $1$, but other configurations can be useful in certain circumstances
    - Using a negative minimum value allows the initial value to not be the lowest value, which might be useful for 2-way counters
    - Using (positive) scale values closer to $0$ allows to increase the amount of stored values beyond $101$
    - Using different minimum/maximum values might reduce the complexity if analog value outputs are wanted by avoiding having to perform transformation of the value ranges
  - The value can be increased/decreased by sending a $\pm 1$ input to the accumulator (the circuit is the same for 1 and 2-way)
  - In some cases, it might be useful to not enable use steps and set the scale to $60 \cdot \text{spacing}$. This allows to change the stored value on each frame, but requires the input to come from a  1 frame pulse generator to get the normal input behaviour
- To add cycle, connect the comparator with the biggest threshold to an AND gate with the positive accumulator input as its second input. Connect that AND gate to an arithmetic logic block set to multiplication with a $-1000$ constant and $0.02$ duration, and that arithmetic logic block to the accumulator
  - The normal input to the accumulator must come from a 1 frame pulse generator
  - If using the circuit as 2-way, repeat the process with the comparator with the smallest threshold and the negative input of the accumulator. The AND gate should have an additional $-2$ always on input (which can be achieved with an arithmetic logic block set to addition with a $-1000$ constant). The arithmetic logic block and input pulse generator can be reused, using negative inputs to reduce the value
- Complexity
  - 1-way: $n+1$
  - 1-way+cycle: $n+4$
  - 2-way: $n+1$
  - 2-way+cycle: $n+5$
- Takes 2 frames to update without cycle, and 3 frames otherwise
- Example blueprints: [\underline{1-way}](https://steamcommunity.com/sharedfiles/filedetails/?id=3309036414), [\underline{1-way+cycle}](https://steamcommunity.com/sharedfiles/filedetails/?id=3309036347), [\underline{2-way}](https://steamcommunity.com/sharedfiles/filedetails/?id=3309036504) and [\underline{2-way+cycle}](https://steamcommunity.com/sharedfiles/filedetails/?id=3309036241)

### Decimal (Base $10$)

#### Logic Gates Implementation

- Works best when $n$ is of the form $c \cdot 10^k$ where $c$ is an integer in the range $[1, 9]$ and k is any positive integer. If it isn't, more gates are needed to cap the value at $n$
- $n = 10^\text{amount of cells}; \text{ amount of cells} = \ceil*{\log_{10} n}$
- Each cell is the general circuit for $n=10$ with cycle and no input gate, except for the last cell which can have any value $2 \leq n \leq 10$ and doesn't need to have cycle
- The output is encoded as a decimal number with a digit stored in each cell (with the first cell being the least significant digit and the last cell being the most significant digit)
- Diagram of the circuit:
\begin{figure}[H]
    \makebox[\textwidth][c]{
    \begin{tikzpicture}
        % Nodes

        % Cell 1
        \node[node]       (ORs_1)                                        {All OR gates\\except the last};
        \node[node]       (OR_final_1)    [below = 1.5mm of ORs_1]       {Last OR gate};
        \node[node]       (ANDs_1)        [below = 1.5mm of OR_final_1]  {All AND gates};
        \coordinate[above=16pt of ORs_1]       (cell_1);

        % Hidden cell 1
        \node[hiddenNode] (ORs_h1)        [right = of ORs_1]             {};
        \node[hiddenNode] (OR_final_h1)   [right = of OR_final_1]        {};
        \node[hiddenNode] (ANDs_h1)       [right = of ANDs_1]            {};

        % Cell k
        \node[node]       (ORs_k)         [right = of ORs_h1]            {All OR gates\\except the last};
        \node[node]       (OR_final_k)    [right = of OR_final_h1]       {Last OR gate};
        \node[node]       (ANDs_k)        [right = of ANDs_h1]           {All AND gates};
        \coordinate[above=16pt of ORs_k]       (cell_k);

        % Cell k+1
        \node[node]       (ORs_k+1)       [right = of ORs_k]             {All OR gates\\except the last};
        \node[node]       (OR_final_k+1)  [right = of OR_final_k]        {Last OR gate};
        \node[node]       (ANDs_k+1)      [right = of ANDs_k]            {All AND gates};
        \coordinate[above=16pt of ORs_k+1]     (cell_k+1);

        % Hidden cell 2
        \node[hiddenNode] (ORs_h2)        [right = of ORs_k+1]           {};
        \node[hiddenNode] (OR_final_h2)   [right = of OR_final_k+1]      {};
        \node[hiddenNode] (ANDs_h2)       [right = of ANDs_k+1]          {};

        % Cell n
        \node[node]       (ORs_n)         [right = of ORs_h2]            {All OR gates\\except the last};
        \node[hiddenNode] (OR_final_n)    [right = of OR_final_h2]       {};
        \node[node]       (ANDs_n)        [right = of ANDs_h2]           {All AND gates};
        \coordinate[above=16pt of ORs_n]       (cell_n);

        % Input gate
        \node[node]       (input_pulse) at ($(ANDs_k) + (0.45, -3.25)$)  {1 frame pulse\\generator (input)};
        \node[node]       (input_OR)      [right = 1.5mm of input_pulse] {OR gate};
        \coordinate[above=16pt of input_pulse] (input_cell);

        % Cell bounding boxes
        \begin{scope}[on background layer]
            \node[node,       fit=(ORs_1)(ANDs_1)(OR_final_1)(cell_1),         label={[anchor=north, yshift=-2.5pt]Cell $1$}] {};
            \node[hiddenNode, fit=(ORs_h1)(ANDs_h1)(OR_final_h1)]              {$\cdots$};
            \node[node,       fit=(ORs_k)(ANDs_k)(OR_final_k)(cell_k),         label={[anchor=north, yshift=-2.5pt]Cell $k$}] {};
            \node[node,       fit=(ORs_k+1)(ANDs_k+1)(OR_final_k+1)(cell_k+1), label={[anchor=north, yshift=-2.5pt]Cell $k+1$}] {};
            \node[hiddenNode, fit=(ORs_h2)(ANDs_h2)(OR_final_h2)]              {$\cdots$};
            \node[node,       fit=(ORs_n)(ANDs_n)(OR_final_n)(cell_n),         label={[anchor=north, yshift=-2.5pt]Cell $\ceil{\log_{10} n}$}] {};
            \node[node,       fit=(input_pulse)(input_OR)(input_cell),         label={[anchor=north, yshift=-2.5pt]Input circuit}] {};
        \end{scope}

        % Arrows

        % Points between cells (top)
        \coordinate (1-h1)    at ($(ORs_1.east)!0.5!(ORs_h1.west)   + (0, 1.75)$);
        \coordinate (h1-k)    at ($(ORs_h1.east)!0.5!(ORs_k.west)   + (0, 1.75)$);
        \coordinate (k-k+1)   at ($(ORs_k.east)!0.5!(ORs_k+1.west)  + (0, 1.75)$);
        \coordinate (k+1-h2)  at ($(ORs_k+1.east)!0.5!(ORs_h2.west) + (0, 1.75)$);
        \coordinate (h2-n)    at ($(ORs_h2.east)!0.5!(ORs_n.west)   + (0, 1.75)$);
        \coordinate (n-)      at ($(ORs_n.east)                     + (0.5, 1.75)$);
        % Points between cells (bottom)
        \coordinate (1-h1-)   at ($(1-h1)                           - (0, 4.6)$);
        \coordinate (h1-k-)   at ($(h1-k)                           - (0, 4.6)$);
        \coordinate (k-k+1-)  at ($(k-k+1)                          - (0, 4.6)$);
        \coordinate (k+1-h2-) at ($(k+1-h2)                         - (0, 4.6)$);
        \coordinate (h2-n-)   at ($(h2-n)                           - (0, 4.6)$);

        % Cell 1
        \draw[line]  (ORs_1.east)         -- (ORs_1 -| 1-h1);
        \draw[arrow] (OR_final_1.east)    -- (OR_final_1 -| 1-h1)      |- (ANDs_h1.west);

        % Hidden cell 1
        \draw[line]  (ORs_h1.east)        -- (ORs_h1 -| h1-k);
        \draw[arrow] (OR_final_h1.east)   -- (OR_final_h1 -| h1-k)     |- (ANDs_k.west);
        \draw[->-]   (ANDs_1 -| 1-h1)     -- (1-h1-) -- (h1-k-)        -- (h1-k |- ANDs_k);
        \draw[->-]   (ORs_1  -| 1-h1)     -- (1-h1)  -- (h1-k)         -- (h1-k |- ORs_k);

        % Cell k
        \draw[line]  (ORs_k.east)         -| (k-k+1);
        \draw[arrow] (OR_final_k.east)    -- (OR_final_k -| k-k+1)     |- (ANDs_k+1.west);
        \draw[->-]   (h1-k-)              -- (k-k+1-);
        \draw[->-]   (h1-k)               -- (k-k+1);
        \draw[line]  (ANDs_k -| k-k+1)    -- (k-k+1-);

        % Cell k+1
        \draw[line]  (ORs_k+1.east)       -| (k+1-h2);
        \draw[arrow] (OR_final_k+1.east)  -- (OR_final_k+1 -| k+1-h2)  |- (ANDs_h2.west);
        \draw[->-]   (k-k+1-)             -- (k+1-h2-);
        \draw[->-]   (k-k+1)              -- (k+1-h2);

        % Hidden cell 2
        \draw[line]  (ORs_h2.east)        -- (ORs_h2 -| h2-n);
        \draw[arrow] (OR_final_h2.east)   -- (OR_final_h2 -| h2-n)     |- (ANDs_n.west);
        \draw[->-]   (ANDs_k+1 -| k+1-h2) -- (k+1-h2-) -- (h2-n-)      -- (h2-n |- ANDs_n);
        \draw[->-]   (k+1-h2)             -- (h2-n);

        % Cell n
        \draw[arrow] (ORs_n.east)         -- (ORs_n -| n-)             |- (input_OR.east);
        \draw[->-]   (ORs_h2 -| h2-n)     -- (h2-n) -- (n-)            -- (n- |- ORs_n);

        % Input gate
        \coordinate  (input) at ($(input_pulse.north) + (0, 1.25)$);
        \draw[arrow] (input_pulse.north)  -- (input)                   -| (ANDs_1.south);
        \draw[line]  (input_OR.north)     |- (input);
        \draw[->-]   (ANDs_1 |- 1-h1-)    -- (1-h1-);
    \end{tikzpicture}
    }
    \caption{Diagram of the decimal method counter (logic gates implementation)}
    \label{fig:CounterDecimal}
\end{figure}
- To add cycle, add cycle to the last cell and remove the OR gate from the input circuit
- To make it 2-way, duplicate the input circuit but connect all OR gates except the first from each cell to the OR gate. Then, make each cell 2-way, connect the cells in the same direction using the first OR gate of each cell rather than the last, and connect the new input circuit to all AND gates for the second direction
- Might require a decoder (unless you want to show numbers on a screen)
  - To create it, take $n$ AND gates and assign a different combination of 1 output gate from each cell to each of them (if you only need to use it combined with other circuits, you can combine all of their decoders into a single one to use less gates)
  - Has a complexity of $n$ gates
- Requires a startup pulse to one of the toggled OR gates on each cell to work (achieved with a 1 frame pulse generator)
- Complexity
  - 1-way: $2 \ceil*{\frac{n}{10^{\ceil*{\log_{10} n} - 1}}} + 20 \ceil*{\log_{10} n} - 19$
  - 1-way+cycle: $2 \ceil*{\frac{n}{10^{\ceil*{\log_{10} n} - 1}}} + 20 \ceil*{\log_{10} n} - 19$
  - 2-way: $3 \ceil*{ \frac{n}{10^{\ceil*{\log_{10} n} - 1}}} + 30 \ceil*{\log_{10} n} - 28$
  - 2-way+cycle: $3 \ceil*{\frac{n}{10^{\ceil*{\log_{10} n} - 1}}} + 30 \ceil*{\log_{10} n} - 28$
- Takes 3 frames to update with cycle and 4 frames otherwise
- Example blueprints: [\underline{1-way}](https://steamcommunity.com/sharedfiles/filedetails/?id=2134491881), [\underline{1-way+cycle}](https://steamcommunity.com/sharedfiles/filedetails/?id=2134492935), [\underline{2-way}](https://steamcommunity.com/sharedfiles/filedetails/?id=2134494676) and [\underline{2-way+cycle}](https://steamcommunity.com/sharedfiles/filedetails/?id=2134496082)

#### Accumulator Implementation

- Works best when $n$ is of the form $c \cdot 10^k$ where $c$ is an integer in the range $[1, 9]$ and k is any positive integer. If it isn't, more gates are needed to cap the value at $n$
- $n = 10^\text{amount of cells}; \text{ amount of cells} = \ceil*{\log_{10} n}$
- Each cell is the general circuit implemented with accumulators for $n=10$ with cycle and without use steps, except for the last cell which can have any value $2 \leq n \leq 10$ and doesn't need to have cycle
- The output is encoded as a decimal number with a digit stored in each cell (with the first cell being the least significant digit and the last cell being the most significant digit)
- Made by connecting the last comparator of each cell to the AND gate from the cycle circuit of all following cells, and the AND gate from the cycle circuit of each cell to the accumulator of the next cell.
  - Requires a different input circuit: the positive input should be in a 1 frame pulse generator connected to an AND gate connected to the accumulator of the first cell and the AND gate from the cycle circuit of all cells. All comparators except the last on each cell should be connected to an OR gate connected to the AND gate from the input circuit
- To add cycle, add cycle to the last cell and remove all the gates in the input circuit except the pulse generator, which should be connected to the accumulator of the first cell and the AND gate from the cycle circuit of all cells
- To make it 2-way, duplicate the input circuit but connect all comparators except the first from each cell to the OR gate. Then, make each cell 2-way, connect the cells in the same direction using the first comparator of each cell rather than the last, and connect the new input circuit to all AND gates for the second direction
  - The AND gate from the second input circuit should have a $-1$ output scale
- Might require a decoder (unless you want to show numbers on a screen)
  - To create it, take $n$ AND gates and assign a different combination of 1 comparator from each cell to each of them (if you only need to use it combined with other circuits, you can combine all of their decoders into a single one to use less gates)
  - Has a complexity of $n$ gates
- Complexity
  - 1-way: $\ceil*{\frac{n}{10^{\ceil*{\log_{10} n} - 1}}} + 13 \ceil*{\log_{10} n} - 9$
  - 1-way+cycle: $\ceil*{\frac{n}{10^{\ceil*{\log_{10} n} - 1}}} + 13 \ceil*{\log_{10} n} - 9$
  - 2-way: $\ceil*{\frac{n}{10^{\ceil*{\log_{10} n} - 1}}} + 14 \ceil*{\log_{10} n} - 7$
  - 2-way+cycle: $\ceil*{\frac{n}{10^{\ceil*{\log_{10} n} - 1}}} + 14 \ceil*{\log_{10} n} - 8$
- Takes 3 frames to update
- Example blueprints: [\underline{1-way}](https://steamcommunity.com/sharedfiles/filedetails/?id=3309037356), [\underline{1-way+cycle}](https://steamcommunity.com/sharedfiles/filedetails/?id=3309037285), [\underline{2-way}](https://steamcommunity.com/sharedfiles/filedetails/?id=3309037105) and [\underline{2-way+cycle}](https://steamcommunity.com/sharedfiles/filedetails/?id=3309037186)

### Binary (Base $2$)

- Works best when $n$ is a power of $2$. If it isn't, more gates are needed to cap the value at $n$
- $n = 2^\text{amount of cells}; \text{ amount of cells} = \ceil*{\log_2 n}$
- The output is encoded as a binary number with a bit stored in each cell (with the first cell being the least significant digit and the last cell being the most significant digit)
- Diagram of the circuit:
\begin{figure}[H]
    \makebox[\textwidth][c]{
    \begin{tikzpicture}
        % Nodes

        % Cell 1
        \node[node]       (OR_1)                                         {Toggled OR gate\\(output)};
        \node[node]       (AND_1)         [below = 5mm of OR_1]          {AND gate};
        \coordinate[above=16pt of OR_1]        (cell_1);

        % Hidden cell 1
        \node[hiddenNode] (OR_h1)         [right = of OR_1]              {};
        \node[hiddenNode] (AND_h1)        [right = of AND_1]             {};

        % Cell k
        \node[node]       (OR_k)          [right = of OR_h1]             {Toggled OR gate\\(output)};
        \node[node]       (AND_k)         [right = of AND_h1]            {AND gate};
        \coordinate[above=16pt of OR_k]        (cell_k);

        % Cell k+1
        \node[node]       (OR_k+1)        [right = of OR_k]              {Toggled OR gate\\(output)};
        \node[node]       (AND_k+1)       [right = of AND_k]             {AND gate};
        \coordinate[above=16pt of OR_k+1]      (cell_k+1);

        % Hidden cell 2
        \node[hiddenNode] (OR_h2)         [right = of OR_k+1]            {};
        \node[hiddenNode] (AND_h2)        [right = of AND_k+1]           {};

        % Cell n
        \node[node]       (OR_n)          [right = of OR_h2]             {Toggled OR gate\\(output)};
        \node[node]       (AND_n)         [right = of AND_h2]            {AND gate};
        \coordinate[above=16pt of OR_n]        (cell_n);

        % Input circuit
        \node[node]       (input_pulse) at ($(AND_k) + (0.45, -2.75)$)   {1 frame pulse\\generator (input)};
        \node[node]       (input_NAND)    [right = 1.5mm of input_pulse] {NAND gate};
        \coordinate[above=16pt of input_pulse] (input_cell);

        % Cell bounding boxes
        \begin{scope}[on background layer]
            \node[node,       fit=(OR_1)(AND_1)(cell_1),                 label={[anchor=north, yshift=-2.5pt]Cell $1$}] {};
            \node[hiddenNode, fit=(OR_h1)(AND_h1)]                       {$\cdots$};
            \node[node,       fit=(OR_k)(AND_k)(cell_k),                 label={[anchor=north, yshift=-2.5pt]Cell $k$}] {};
            \node[node,       fit=(OR_k+1)(AND_k+1)(cell_k+1),           label={[anchor=north, yshift=-2.5pt]Cell $k+1$}] {};
            \node[hiddenNode, fit=(OR_h2)(AND_h2)]                       {$\cdots$};
            \node[node,       fit=(OR_n)(AND_n)(cell_n),                 label={[anchor=north, yshift=-2.5pt]Cell $\ceil{\log_2 n}$}] {};
            \node[node,       fit=(input_pulse)(input_NAND)(input_cell), label={[anchor=north, yshift=-2.5pt]Input Circuit}] {};
        \end{scope}

        % Arrows

        % Points between cells
        \coordinate (1-h1)   at ($(OR_1.east)!0.5!(OR_h1.west)   + (0, 1.75)$);
        \coordinate (h1-k)   at ($(OR_h1.east)!0.5!(OR_k.west)   + (0, 1.75)$);
        \coordinate (k-k+1)  at ($(OR_k.east)!0.5!(OR_k+1.west)  + (0, 1.75)$);
        \coordinate (k+1-h2) at ($(OR_k+1.east)!0.5!(OR_h2.west) + (0, 1.75)$);
        \coordinate (h2-n)   at ($(OR_h2.east)!0.5!(OR_n.west)   + (0, 1.75)$);
        \coordinate (n-)     at ($(OR_n.east)                    + (0.5, 1.75)$);

        % Cell 1
        \draw[arrow] (AND_1.north)       -- (OR_1.south);
        \draw[arrow] (OR_1.east)         -- (OR_1 -| 1-h1)     |- (AND_h1.west);

        % Hidden cell 1
        \draw[arrow] (OR_h1.east)        -- (OR_h1 -| h1-k)    |- (AND_k.west);
        \draw[->-]   (OR_1 -| 1-h1)      -- (1-h1) -- (h1-k)   -- (h1-k |- OR_k);

        % Cell k
        \draw[arrow] (AND_k.north)       -- (OR_k.south);
        \draw[arrow] (OR_k.east)         -- (OR_k -| k-k+1)    |- (AND_k+1.west);
        \draw[->-]   (h1-k)              -- (k-k+1);
        \draw[line]  (OR_k -| k-k+1)     -- (k-k+1);

        % Cell k+1
        \draw[arrow] (AND_k+1.north)     -- (OR_k+1.south);
        \draw[arrow] (OR_k+1.east)       -- (OR_k+1 -| k+1-h2) |- (AND_h2.west);
        \draw[->-]   (k-k+1)             -- (k+1-h2);
        \draw[line]  (OR_k+1 -| k+1-h2)  -- (k+1-h2);

        % Hidden cell 2
        \draw[arrow] (OR_h2.east)        -- (OR_h2 -| h2-n)    |- (AND_n.west);
        \draw[->-]   (k+1-h2)            -- (h2-n);

        % Cell n
        \draw[arrow] (AND_n.north)       -- (OR_n.south);
        \draw[arrow] (OR_n.east)         -- (OR_n -| n-)       |- (input_NAND.east);
        \draw[->-]   (OR_h2 -| h2-n)     -- (h2-n) -- (n-)     -- (n- |- OR_n);

        \coordinate  (input) at ($(input_pulse.north) + (0, 1.25)$);
        \draw[->-]   (input_pulse.north) -- (input);
        \draw[->-]   (input_NAND.north)  -- (input_NAND |- input);
        \draw[arrow] (input)             -| (AND_1.south);
        \draw[arrow] (input)             -| (AND_n.south);
        \draw[arrow] (input -| AND_h1)   -| (AND_h1.south);
        \draw[arrow] (input -| AND_k)    -| (AND_k.south);
        \draw[arrow] (input -| AND_k+1)  -| (AND_k+1.south);
        \draw[arrow] (input -| AND_h2)   -| (AND_h2.south);
    \end{tikzpicture}
    }
    \caption{Diagram of the binary method counter}
    \label{fig:CounterBinary}
\end{figure}
- To add cycle, remove the NAND gate from the input circuit and the AND gate on the first cell, and connect the 1 frame pulse generator directly to the OR gate in the first cell
- To make it 2-way, add a NOT gate to each cell with its OR gate as input and a new AND gate connected in the same way as the old AND gate, but using the NOT gate of all previous cells as input instead of the OR gates. Then replace the NAND gate on the input circuit with an OR gate and duplicate it (with the copy being connected to the new AND gates on each cell rather than the old ones). Connect the NOT gate of all the cells to the OR gate of the first input circuit, and the OR gate of all the cells to the OR gate of the second input circuit
- Might require a decoder
  - To create it, add a NOT gate to each cell and replace the NAND gate of the input circuit with an OR gate like in the 2-way version (if using the 1-way versions). Then, take $n$ AND gates and assign each of them a different combination of either the OR or NOT from each cell (if you only need to use it combined with other circuits you can combine all of their decoders into a single one to use less gates)
  - Has a complexity of $\ceil*{\log_2 n} + n - 1$ for the 1-way version, $\ceil*{\log_2 n} + n$ for the 1-way+cycle version and $n$ for the 2-way versions
- Complexity
  - 1-way: $2 \ceil*{\log_2 n} + 3$
  - 1-way+cycle: $2 \ceil*{\log_2 n}$
  - 2-way: $4 \ceil*{\log_2 n} + 4$
  - 2-way+cycle: $4 \ceil*{\log_2 n}$
- Takes 3 frames to update for 1-way+cycle and 4 frames otherwise
- Example blueprints: [\underline{1-way}](https://steamcommunity.com/sharedfiles/filedetails/?id=2134497489), [\underline{1-way+cycle}](https://steamcommunity.com/sharedfiles/filedetails/?id=2134498845), [\underline{2-way}](https://steamcommunity.com/sharedfiles/filedetails/?id=2134500019) and [\underline{2-way+cycle}](https://steamcommunity.com/sharedfiles/filedetails/?id=2134500705)

### When to use each method

\begin{table}[H]
    \centering
    \begin{tabular}{|c|m{20em}|m{20em}|}
        \cline{2-3}
        \multicolumn{1}{c|}{} & \thead{1-way} & \thead{2-way} \\
        \hline
        % In order for the "Without cycle" text to be properly vertically centered, the other columns need to be vertically centered as well. To make them look like they are aligned at the top, they must have the same amount of lines
        \rotatebox[origin=c]{90}{\thead{Without cycle}}
        &
        Doesn't require an individual decoder:
        \begin{itemize}
            \item For $n \leq 10$ use the general circuit
            \item For $n > 10$ use the binary circuit
            \newline
        \end{itemize}
        Show values directly on a screen (only numbers for $n > 18$):
        \begin{itemize}
            \item For $n \leq 18$ use the general circuit
            \item For $n > 18$ use the decimal circuit
        \end{itemize}
        &
        Doesn't require an individual decoder:
        \begin{itemize}
            \item For $n \leq 23$ use the general circuit
            \item For $n > 23$ use either the binary or decimal circuit
        \end{itemize}
        Show values directly on a screen (only numbers for $n > 23$):
        \begin{itemize}
            \item For $n \leq 23$ use the general circuit
            \item For $n > 23$ use the decimal circuit
        \end{itemize} \\
        \hline
        \rotatebox[origin=c]{90}{\thead{With cycle}}
        &
        Doesn't require an individual decoder:
        \begin{itemize}
            \item Use the binary circuit
            \newline
            \newline
            \newline
            \newline
            \newline
        \end{itemize}
        Show values directly on a screen (only numbers for $n > 15$):
        \begin{itemize}
            \item For $n \leq 15$ use the general circuit
            \item For $n > 15$ use the decimal circuit
            \newline
        \end{itemize}
        &
        Doesn't require an individual decoder:
        \begin{itemize}
            \item For $n = 4$, $n = 8$, or $11 < n \leq 32$ use the binary circuit
            \item For $4 < n \leq 7$ or $8 < n \leq 11$ use the general circuit
            \item For $n > 32$ use either the binary or decimal circuit
        \end{itemize}
        Show values directly on a screen (only numbers for $n > 17$):
        \begin{itemize}
            \item For $n \leq 17$ use the general circuit
            \item For $n > 17$ use the decimal circuit
            \newline
        \end{itemize} \\
        \hline
    \end{tabular}
    \caption{Comparison of the different counter methods}
    \label{table:CounterComparison}
\end{table}

Notes:

- Calculated for $n \geq 3$, for $n = 2$ a latch is always best
- All mentions of the general and decimal circuits refer to the accumulator-based implementations
- If an individual decoder is needed, the general circuit is always best
- For 2-way without an individual decoder, decimal and binary circuits are very close in terms of complexity. Use their complexity formulas to figure out which is best for your use case. If they have the same complexity, decimal is better due to being faster
- This is just based on the amount of logic gates each circuit uses (unless there is a tie, in which case update speed is used). However, the amount of time it takes for the system to update might also matter depending on the situation. In that case, refer to the circuits' descriptions to compare the speed for the needed use case

## No-Delay Signal Toggle

- Allows to enable/disable an analog signal without increasing the signal delay like normal AND/XOR gate methods do
- Doesn't work when the output block is an AND/XOR gate
- Commonly used to enable/disable angle sensor stabilization (with the input being angle sensors and the output helicopter engines)
\begin{figure}[H]
    \centering
    \begin{tikzpicture}[wideNode/.style={node, minimum width=30.5mm}]
        % Nodes

        \node[wideNode] (input_signal)                                 {Input Signals\\(can be multiple\\blocks)};
        \node[wideNode] (output_blocks) [right = 5cm of input_signal]  {Output Blocks};
        \node[wideNode] (input_toggle1) [above = 1.5cm of input_signal]  {OR Gate with\\toggle keybind};
        \node[wideNode] (input_toggle2) [right = 5cm of input_toggle1] {OR Gate with\\toggle keybind};
        \coordinate (toggles)    at ($(input_toggle1)!0.5!(input_toggle2)$);
        \coordinate (middle)     at ($(input_signal)!0.5!(input_toggle1)$);
        \node[node] (XOR)        at (toggles |- middle) {XOR gate with\\-1 output scale};

        % Arrows

        \draw[arrow] (input_signal.east)  -- (output_blocks.west);
        \draw[arrow] (input_signal.north) |- (XOR.west);
        \draw[arrow] (XOR.east)           -| (output_blocks.north);
        \draw[->-]   (input_toggle1.east) -- (toggles);
        \draw[->-]   (input_toggle2.west) -- (toggles);
        \draw[arrow] (toggles)            -- (XOR.north);
    \end{tikzpicture}
    \caption{Diagram of the no-delay signal toggle}
    \label{fig:NoDelaySignalToggle}
\end{figure}
- [\underline{Example blueprint}](https://steamcommunity.com/sharedfiles/filedetails/?id=3054610284)

\clearpage

# Historical Circuits

This section contains old circuits that were previously in \nameref{useful-circuits}, but have since been superseded by new blocks. They are kept here for historical reasons and in case they are seen in older circuits.

## NOR/NOT Gate

- Superseded by the NOR gate block
- Inverts the state of the input
- Made by connecting an always on input (a sensor that is always on, all sensors can be configured to work like this but the most commonly used one is a distance sensor with 0 range and invert trigger) to a XOR gate
- If it only has a single input that isn't the always on input it will act as a NOT gate, if it has multiple it will act as a NOR gate
- You can make a NAND/XNOR gate by making an AND/XOR gate output to a NOT gate and taking the output from the NOT gate

## Feedback Loop

- Superseded by accumulators
- Allows to store an analog value in the range $[-1, 1]$
- Made by connecting 2 OR gates to each other, connecting the input to both of them, and taking the output from one of them
- Each frame the input is active, the stored value increases by the value of the input
  - With a close to 0 input value, the circuit can be used to generate an analog output value
- One of the OR gates can be replaced with an XOR to add a reset input. In this case, the normal input should go to the OR gate only and the reset input must be 2 different signals connected to the XOR gate
  - Be aware that this method adds a 1 frame jitter to the stored signal

### Clamped Feedback Loop

- Allows limiting the output signal of a feedback loop to the range $[0, 1]$
- Diagram of the circuit:
\begin{figure}[H]
    \centering
    \begin{tikzpicture}[trim left=-12em]
        % Nodes

        \node[node] (feedback_top)                               {OR Gate};
        \node[node] (feedback_bot) [below = 5mm of feedback_top] {OR Gate};
        \coordinate (feedback_mid) at ($(feedback_top.west)!0.5!(feedback_bot.west)$);

        \node[node] (input)        [left = of feedback_mid]      {Input Signal};

        \coordinate[above=16pt of feedback_top] (feedback);

        \node[node] (C+1)          [right = of feedback_bot]     {Always on sensor};
        \node[node] (C-1)          [above = 5mm of C+1]          {OR gate with\\-1 output scale};

        \node[node] (output)       [right = of C-1]              {OR gate with\\-1 output scale\\(output)};

        % Bounding boxes
        \begin{scope}[on background layer]
            \node[node, fit=(feedback_top)(feedback_bot)(feedback), label={[anchor=north, yshift=-2.5pt]Feedback Loop}] {};
        \end{scope}

        % Arrows

        \coordinate  (input_split) at ($(input.east) + (0.5, 0)$);
        \draw[line]  (input.east)                    -- (input_split);
        \draw[arrow] (input_split)                   |- (feedback_top.west);
        \draw[arrow] (input_split)                   |- (feedback_bot.west);
        \draw[arrow] ($(feedback_bot.north) + (2mm, 0)$)  -- ($(feedback_top.south) + (2mm, 0)$);
        \draw[arrow] ($(feedback_top.south) + (-2mm, 0)$) -- ($(feedback_bot.north) + (-2mm, 0)$);
        \draw[arrow] (C+1.west)                      -- (feedback_bot.east);
        \draw[arrow] (C+1.north)                     -- (C-1.south);
        \draw[arrow] (C-1.west |- feedback_top.east) |- (feedback_top.east);
        \draw[arrow] (C-1.east)                      -- (output.west);
        \draw[arrow] (feedback_bot.south) -- ($(feedback_bot.south) + (0, -0.5)$) -| (output);
    \end{tikzpicture}
    \caption{Diagram of the clamped feedback loop}
    \label{fig:ClampedFeedbackLoop}
\end{figure}
- Note: the input signal is reversed. This means that a negative value increases the stored value while a positive value decreases it
- Keybinds can be added to one of the OR gates in the feedback loop to go to the max/min value. The green keybind sets the min value while the red keybind sets the max value
- [\underline{Example blueprint}](https://steamcommunity.com/sharedfiles/filedetails/?id=2911246646)
- Python script to simulate the behaviour (input value is automatically reversed):

  ```python
  def clamp(x: float) -> float:
      return min(max(x, -1), 1)

  def update(
      feedback_top: float, feedback_bottom: float, user_input: float
  ) -> tuple[float, float]:
      return (
          clamp(feedback_bottom - user_input + 1),
          clamp(feedback_top    - user_input - 1)
      )

  def print_state(feedback_top: float, feedback_bottom: float):
      print("State:")
      print("\tFeedback Top: ", round(feedback_top, 3))
      print("\tFeedback Bottom: ", round(feedback_bottom, 3))
      print("Output: ", round(clamp(-(feedback_top - 1)), 3))

  feedback_top, feedback_bottom = 1, 0
  print_state(feedback_top, feedback_bottom)
  while True:
      user_input = input("Enter input (between -1 and 1, Q to quit): ")
      if user_input.lower() == "q":
          break
      try:
          user_input = float(user_input)
      except ValueError:
          print("Invalid input. Try again")
          continue
      feedback_top, feedback_bottom = update(feedback_top, feedback_bottom, user_input)
      print_state(feedback_top, feedback_bottom)
  ```

- Credits to Precache for figuring out this circuit

\clearpage
\appendix

# Input value to multiplier for hinges/wings with control surfaces {#InputValueMultiplier}

Due to a bug, the angle by which hinges/wings/other blocks with the "steering help" setting rotate doesn't scale linearly with the input value. This section contains the multipliers used for many input values, found experimentally. Some notes about this process:

- Final angle is the resulting angle of the hinge measured with a max angle of $90$ degrees and an error of $\pm 0.005$ degrees
- The multiplier was calculated with $\text{multiplier} = \frac{\text{final angle}}{90}$
- A calculator using this data made by confusionextended can be found [\underline{here}](https://www.desmos.com/calculator/i70yadxqyy)

\begin{longtable}{|c|c|c !{\vrule width 3pt} c|c|c !{\vrule width 3pt} c|c|c|}
    \hline
    \thead{Input\\value} & \thead{Final\\angle} & \thead{Multiplier} & \thead{Input\\value} & \thead{Final\\angle} & \thead{Multiplier} & \thead{Input\\value} & \thead{Final\\angle} & \thead{Multiplier} \\
    \HLine{2pt}
    1.000 & 90.000 & 1.00000000 & 0.810 & 31.275 & 0.34750000 & 0.480 & 05.640 & 0.06266667 \\
    \hline
    0.995 & 88.345 & 0.98161111 & 0.805 & 30.215 & 0.33572222 & 0.470 & 05.295 & 0.05883333 \\
    \hline
    0.990 & 86.680 & 0.96311111 & 0.800 & 29.195 & 0.32438889 & 0.460 & 04.960 & 0.05511111 \\
    \hline
    0.985 & 85.000 & 0.94444444 & 0.795 & 28.225 & 0.31361111 & 0.450 & 04.640 & 0.05155556 \\
    \hline
    0.980 & 83.315 & 0.92572222 & 0.790 & 27.300 & 0.30333333 & 0.440 & 04.335 & 0.04816667 \\
    \hline
    0.975 & 81.620 & 0.90688889 & 0.785 & 26.425 & 0.29361111 & 0.430 & 04.040 & 0.04488889 \\
    \hline
    0.970 & 79.920 & 0.88800000 & 0.780 & 25.595 & 0.28438889 & 0.420 & 03.760 & 0.04177778 \\
    \hline
    0.965 & 78.215 & 0.86905556 & 0.775 & 24.825 & 0.27583333 & 0.410 & 03.495 & 0.03883333 \\
    \hline
    0.960 & 76.505 & 0.85005556 & 0.770 & 24.105 & 0.26783333 & 0.400 & 03.245 & 0.03605556 \\
    \hline
    0.955 & 74.795 & 0.83105556 & 0.765 & 23.440 & 0.26044444 & 0.390 & 03.005 & 0.03338889 \\
    \hline
    0.950 & 73.085 & 0.81205556 & 0.760 & 22.830 & 0.25366667 & 0.380 & 02.775 & 0.03083333 \\
    \hline
    0.945 & 71.380 & 0.79311111 & 0.755 & 22.280 & 0.24755556 & 0.370 & 02.560 & 0.02844444 \\
    \hline
    0.940 & 69.675 & 0.77416667 & 0.750 & 21.790 & 0.24211111 & 0.360 & 02.355 & 0.02616667 \\
    \hline
    0.935 & 67.975 & 0.75527778 & 0.740 & 20.920 & 0.23244444 & 0.350 & 02.160 & 0.02400000 \\
    \hline
    0.930 & 66.280 & 0.73644444 & 0.730 & 20.075 & 0.22305556 & 0.340 & 01.980 & 0.02200000 \\
    \hline
    0.925 & 64.595 & 0.71772222 & 0.720 & 19.255 & 0.21394444 & 0.330 & 01.805 & 0.02005556 \\
    \hline
    0.920 & 62.920 & 0.69911111 & 0.710 & 18.460 & 0.20511111 & 0.320 & 01.645 & 0.01827778 \\
    \hline
    0.915 & 61.250 & 0.68055556 & 0.700 & 17.685 & 0.19650000 & 0.310 & 01.495 & 0.01661111 \\
    \hline
    0.910 & 59.595 & 0.66216667 & 0.690 & 16.930 & 0.18811111 & 0.300 & 01.350 & 0.01500000 \\
    \hline
    0.905 & 57.955 & 0.64394444 & 0.680 & 16.200 & 0.18000000 & 0.290 & 01.220 & 0.01355556 \\
    \hline
    0.900 & 56.325 & 0.62583333 & 0.670 & 15.490 & 0.17211111 & 0.280 & 01.095 & 0.01216667 \\
    \hline
    0.895 & 54.715 & 0.60794444 & 0.660 & 14.800 & 0.16444444 & 0.270 & 00.980 & 0.01088889 \\
    \hline
    0.890 & 53.125 & 0.59027778 & 0.650 & 14.135 & 0.15705556 & 0.269 & 00.970 & 0.01077778 \\
    \hline
    0.885 & 51.550 & 0.57277778 & 0.640 & 13.485 & 0.14983333 & 0.268 & 00.955 & 0.01061111 \\
    \hline
    0.880 & 49.995 & 0.55550000 & 0.630 & 12.860 & 0.14288889 & 0.267 & 00.945 & 0.01050000 \\
    \hline
    0.875 & 48.465 & 0.53850000 & 0.620 & 12.250 & 0.13611111 & 0.266 & 00.935 & 0.01038889 \\
    \hline
    0.870 & 46.960 & 0.52177778 & 0.610 & 11.660 & 0.12955556 & 0.265 & 00.925 & 0.01027778 \\
    \hline
    0.865 & 45.480 & 0.50533333 & 0.600 & 11.095 & 0.12327778 & 0.264 & 00.915 & 0.01016667 \\
    \hline
    0.860 & 44.025 & 0.48916667 & 0.590 & 10.545 & 0.11716667 & 0.263 & 00.905 & 0.01005556 \\
    \hline
    0.855 & 42.600 & 0.47333333 & 0.580 & 10.010 & 0.11122222 & 0.262 & 00.000 & 0.00000000 \\
    \hline
    0.850 & 41.200 & 0.45777778 & 0.570 & 09.500 & 0.10555556 & 0.261 & 00.000 & 0.00000000 \\
    \hline
    0.845 & 39.830 & 0.44255556 & 0.560 & 09.000 & 0.10000000 & 0.260 & 00.000 & 0.00000000 \\
    \hline
    0.840 & 38.505 & 0.42783333 & 0.550 & 08.525 & 0.09472222 & 0.250 & 00.000 & 0.00000000 \\
    \hline
    0.835 & 37.200 & 0.41333333 & 0.540 & 08.065 & 0.08961111 & 0.200 & 00.000 & 0.00000000 \\
    \hline
    0.830 & 35.940 & 0.39933333 & 0.530 & 07.620 & 0.08466667 & 0.150 & 00.000 & 0.00000000 \\
    \hline
    0.825 & 34.715 & 0.38572222 & 0.520 & 07.190 & 0.07988889 & 0.100 & 00.000 & 0.00000000 \\
    \hline
    0.820 & 33.530 & 0.37255556 & 0.510 & 06.780 & 0.07533333 & 0.050 & 00.000 & 0.00000000 \\
    \hline
    0.815 & 32.380 & 0.35977778 & 0.500 & 06.380 & 0.07088889 & 0.000 & 00.000 & 0.00000000 \\
    \hline
    \multicolumn{3}{c!{\vrule width 3pt}}{} & 0.490 & 06.005 & 0.06672222 & \multicolumn{3}{c}{} \\
    \cmidrule(l{-3pt}){4-6}
    \caption{Raw data of the input value multiplier for hinges}
    \label{table:InputValueMultiplierData}
\end{longtable}

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        \begin{axis}[
            height=27.5em,
            width=42em,
            title={Multiplier as a function of the input value},
            xlabel={Input value},
            ylabel={Multiplier},
            domain = 0:1,
            xmin=0, xmax=1,
            ymin=0, ymax=1,
            minor tick num=1,
            grid=both
        ]
            \addplot[color=blue, mark=*] file {data/Input_Value_Multiplier.dat} node[above left, midway] {$f(x)$};
            \addplot[color=black, samples=3] {x} node[above left, midway] {$y=x$};
        \end{axis}
    \end{tikzpicture}
    \caption{Graph of the multiplier for hinges as a function of the input value}
    \label{fig:InputValueMultiplierGraph}
\end{figure}

\clearpage

# Input value to speed for servos with hold position {#InputValueSpeed}

Due to a bug, the speed at which rotating servos with hold position rotate changes with the input value when small values are used. This section contains the speed multipliers used for many input values, found experimentally. Some notes about this process:

- Speed is the speed required on a servo with an input value of $1$ to match the speed of a servo with a speed being tested and the given input value, with an error of $\pm 0.00005$ speed units for speeds lower than $1$ and an error of $\pm 0.0005$ otherwise
  - To compare the speeds of the servos, they were run continuously for some time and the final angle was compared
    - For servos with $360$ angle, they were run for $3$ min
    - For servos with less than $360$ angle, they were run for slightly less than the time required to reach the maximum angle
  - Servos with a configured speed of $1$ and $2$ as well as $360$ and $180$ angle were tested, with other speeds/angles being checked to verify the results
- $f_{s, a}(x)$ represents the resulting speed of a servo with $s$ speed, $a$ angle, and $x$ input value
- The resulting speed can be approximated by $\hat{f}_{s, a}(x) = \min\left(\frac{x}{0.0611} \cdot \frac{a}{90}, s\right)$

\begin{figure}[H]
    \centering
    \begin{tikzpicture}
        \begin{axis}[
            height=27.5em,
            width=42em,
            title={Speed as a function of the input value},
            xlabel={Input value},
            ylabel={Speed},
            domain = 0:0.0625,
            xmin=0, xmax=0.0625,
            ymin=0, ymax=2,
            minor tick num=1,
            grid=both,
            xticklabel style={
                /pgf/number format/fixed,
                /pgf/number format/precision = 3
            },
            scaled x ticks = false
        ]
            \addplot[color=blue, mark=*] file {data/Input_Value_Speed1_360.dat} node[above left, midway] {$f_{1, 360}(x)$};
            \addplot[color=red, mark=*]  file {data/Input_Value_Speed2_360.dat} node[above left, midway] {$f_{2, 360}(x)$};
            \addplot[color=Green, mark=*]  file {data/Input_Value_Speed1_180.dat} node[below right, midway] {$f_{1, 180}(x)$};
            \addplot[color=DodgerBlue, mark=*]  file {data/Input_Value_Speed2_180.dat} node[below right, midway] {$f_{2, 180}(x)$};
            \addplot[color=black, samples=3] {x/0.0611 * 360/90} node[above left, pos=0.35] {$y=\frac{x}{0.0611} \cdot \frac{360}{90}$};
            \addplot[color=black, samples=3] {x/0.0611 * 180/90} node[below right, pos=0.75] {$y=\frac{x}{0.0611} \cdot \frac{180}{90}$};
        \end{axis}
    \end{tikzpicture}
    \caption{Graph of the speed for servos with hold position as a function of the input value}
    \label{fig:InputValueSpeedGraph}
\end{figure}

\clearpage

\begin{longtable}{|c|c !{\vrule width 3pt} c|c !{\vrule width 3pt} c|c !{\vrule width 3pt} c|c|}
    \hline
    \multicolumn{4}{|c !{\vrule width 3pt}}{\thead{Angle 180}} &
    \multicolumn{4}{c|}{\thead{Angle 360}} \\
    \HLine{2pt}
    \multicolumn{2}{|c !{\vrule width 3pt}}{\thead{Speed 1}} &
    \multicolumn{2}{c !{\vrule width 3pt}}{\thead{Speed 2}} &
    \multicolumn{2}{c !{\vrule width 3pt}}{\thead{Speed 1}} &
    \multicolumn{2}{c|}{\thead{Speed 2}} \\
    \HLine{2pt}
    \thead{Input\\value} & \thead{Final\\speed} & \thead{Input\\value} & \thead{Final\\speed} & \thead{Input\\value} & \thead{Final\\speed} & \thead{Input\\value} & \thead{Final\\speed} \\
    \HLine{2pt}
    0.0625 & 1.0000 & 0.0625 & 2.0000 & 0.0625 & 1.0000 & 0.0625 & 2.0000 \\
    \hline
    0.0306 & 1.0000 & 0.0611 & 2.0000 & 0.0153 & 1.0000 & 0.0306 & 2.0000 \\
    \hline
    0.0305 & 0.9980 & 0.0610 & 1.996  & 0.0152 & 0.9950 & 0.0305 & 1.996  \\
    \hline
    0.0304 & 0.9950 & 0.0608 & 1.990  & 0.0151 & 0.9885 & 0.0304 & 1.990  \\
    \hline
    0.0303 & 0.9920 & 0.0606 & 1.984  & 0.0150 & 0.9821 & 0.0303 & 1.984  \\
    \hline
    0.0302 & 0.9885 & 0.0604 & 1.977  & 0.0140 & 0.9175 & 0.0302 & 1.977  \\
    \hline
    0.0301 & 0.9855 & 0.0602 & 1.971  & 0.0130 & 0.8528 & 0.0301 & 1.971  \\
    \hline
    0.0300 & 0.9821 & 0.0600 & 1.965  & 0.0120 & 0.7880 & 0.0300 & 1.965  \\
    \hline
    0.0275 & 0.9000 & 0.0550 & 1.806  & 0.0110 & 0.7231 & 0.0275 & 1.806  \\
    \hline
    0.0250 & 0.8175 & 0.0500 & 1.645  & 0.0100 & 0.6580 & 0.0250 & 1.645  \\
    \hline
    0.0225 & 0.7360 & 0.0450 & 1.475  & 0.0090 & 0.5928 & 0.0225 & 1.485  \\
    \hline
    0.0200 & 0.6540 & 0.0400 & 1.313  & 0.0080 & 0.5275 & 0.0200 & 1.323  \\
    \hline
    0.0175 & 0.5730 & 0.0350 & 1.146  & 0.0070 & 0.4620 & 0.0175 & 1.161  \\
    \hline
    0.0150 & 0.4910 & 0.0300 & 0.9830 & 0.0060 & 0.3964 & 0.0150 & 0.9973 \\
    \hline
    0.0125 & 0.4090 & 0.0250 & 0.8200 & 0.0050 & 0.3306 & 0.0125 & 0.8332 \\
    \hline
    0.0100 & 0.3265 & 0.0200 & 0.6550 & 0.0040 & 0.2648 & 0.0100 & 0.6683 \\
    \hline
    0.0075 & 0.2455 & 0.0150 & 0.4910 & 0.0030 & 0.1987 & 0.0075 & 0.5024 \\
    \hline
    0.0050 & 0.1634 & 0.0100 & 0.3280 & 0.0020 & 0.1326 & 0.0050 & 0.3358 \\
    \hline
    0.0025 & 0.0816 & 0.0050 & 0.1636 & 0.0010 & 0.0663 & 0.0025 & 0.1682 \\
    \hline
    0.0000 & 0.0000 & 0.0000 & 0.0000 & 0.0000 & 0.0000 & 0.0000 & 0.0000 \\
    \hline
    \caption{Raw data of the input value speed for servos with hold position}
    \label{table:InputValueSpeedData}
\end{longtable}

\clearpage

# Tips

- If you want your system to not modify the input that passes through, configure all of the logic gates to have an output scale of 1. Then, if a gate needs to have more inputs other than the original one, make sure the sum of the output values of those other inputs is 0. This will make the output which reaches whatever your system activates be the output that the sensors/keybinds had
- Arithmetics logic blocks can be used to get always on signals with a constant value by using the addition operation with the desired constant. The signal can be toggled by setting the operation to multiplication and using a $+1$ input
- Organize the logic gates on a testbed while working with them before adding them to your vehicle, having the logic gates organized as opposed to scattered across your entire vehicle will make remembering what each gate does easier. There is no wrong way to organize them as long as they aren't randomly placed, but the way I do it is by splitting the gates into groups depending on function, inside each group the arrows of logic gates point towards the outputs of that gate and away from its inputs, then I put the groups of logic gates that a group outputs to in the direction the arrows of the logic gates that the group uses as output are pointing, while I put the groups that group uses as input in the opposite direction
- If you have problems figuring out how to do something with logic gates, draw it on paper first, being able to see all connections at once helps a lot. Another method is writing it with if statements as they translate to logic gates easily (each logic gate is an individual if statement)
- If you still have any questions or need help with something, feel free to contact me on the [\underline{official Trailmakers discord server}](https://discord.gg/trailmakers)

## \large Made by ALVAROPING1 {.unlisted .unnumbered}
