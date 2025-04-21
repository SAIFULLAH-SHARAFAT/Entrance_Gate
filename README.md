# Entrance_Gate
A short RFID scanner and an IR-directed entrance gate program and its description.

# RFID Gate Control System Logic Table (Final Updated)

| **Condition**                                      | **Action**                                                       | **Description**                                                                                             |
|----------------------------------------------------|------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| **Card Scanned at Open Side**                      | Gate opens if Exit IR is HIGH; closes if Exit IR is LOW          | When a valid card is scanned at the open side, the system checks if a trespasser is at the exit. If the exit IR is LOW (indicating a person at the exit), the gate closes. |
| **Simultaneous Card Scans at Both Scanners**       | Gate does not open                                               | If two cards are scanned within 100ms at both scanners, the system prevents the gate from opening to avoid dual-entry collision. |
| **Timeout (No Movement Detected)**                | Gate closes after 60 seconds if no movement is detected           | If no movement is detected (i.e., both IR sensors stay in the same state and that state is High, which means no person), the gate will close after a 60-second timeout to prevent it from staying open unnecessarily. |
| **Normal Entry Sequence (Open and Exit IR)**      | Gate opens if Open IR is HIGH or LOW; closes if Exit IR is LOW   | For normal entry, the Open IR can start as either HIGH or LOW, but the gate will remain open unless the Exit IR becomes LOW (indicating a trespasser at the exit). |
| **Wrong Direction Entry (Open or Exit Side)**     | Gate closes if Exit IR triggers before Open IR or Open IR triggers before Exit IR | If a person attempts to enter in the wrong direction, the system checks if the Open IR is triggered before the Exit IR (or vice versa), and if so, the gate will close immediately to prevent unauthorized entry. |
| **Hesitation or Obstruction**                     | Gate closes and logs anomaly if no movement for too long         | If the user stands still for too long in front of the open IR (or between the IR sensors), the gate closes to prevent it from staying open and logs the event as a potential anomaly. |
| **Partial Pass Detection (Exit IR Timeout)**      | Gate closes if Exit IR does not get triggered within the allowed time | If the exit-side IR remains LOW for an extended period after a card scan (indicating a partial pass), the gate will close after a timeout (e.g., 60 seconds). |
| **Dual-Entry Collision Detection**                | Gate does not open if two cards are scanned within 100ms         | The system blocks the gate from opening if two cards are scanned too quickly at both scanners (within 100ms), preventing dual-entry. |
| **Tailgating Detection (Open Side or Exit Side)**  | Gate closes if multiple signals are detected at Open IR or Exit IR before the other IR triggers | If the **Open IR** detects multiple signals (indicating multiple users entering at once) before the **Exit IR** triggers, or the **Exit IR** detects multiple signals before the **Open IR** triggers, the gate will close to prevent unauthorized entry. |
| **Exit Card Scanned (Exit Side)**                 | Gate remains open if Exit IR is triggered; closes if Open IR is triggered | When a card is scanned at the exit side, the gate will remain open regardless of whether the Exit IR is HIGH or LOW. The gate will only close if the **Open IR** detects someone trying to enter from the wrong side. |
| **Exit Side IR Sequence**                         | Gate closes if Exit IR is not triggered after the scan           | If the Exit IR is not triggered (indicating the user did not pass fully through the gate), the system will close the gate to ensure security. |
| **Timeout After Scan**                            | Gate closes after timeout if user doesn't pass completely        | If the user scans their card but does not pass through the gate within a certain time frame, the gate will close after the timeout to prevent it from staying open unnecessarily. |




# Flowchart for Gate Entry/Exit System

```mermaid
flowchart TD
    A[Start] --> B[Card Scanned at Entry or Exit]
    B --> C{Check Card Scan}
    C -->|Entry Side| D[Check Exit IR is HIGH]
    D -->|True| E[Open Gate for Entry]
    E --> F[Gate Opened for 5 Seconds]
    F --> G[Close Gate]
    G --> H{No Movement for 60 Seconds}
    H -->|Timeout| I[Close Gate]
    C -->|Exit Side| J[Check Entry IR is HIGH]
    J -->|True| K[Open Gate for Exit]
    K --> F
    C -->|Invalid Card Scan| L[Gate Does Not Open]
    D -->|False| L
    J -->|False| L
    L --> M[Log Invalid Entry/Exit]

    %% Additional logic
    F --> N[Detect Tailgating - Multiple IR Signals]
    N -->|Detected| O[Close Gate]
    O --> P[Log Tailgating Event]
    N -->|Not Detected| H

    %% Anomalies and Obstruction Detection
    B --> Q[Detect Hesitation/Obstruction]
    Q -->|Detected| R[Log Anomaly and Close Gate]
    R --> M

    %% Edge cases
    H --> S[Log Timeout and Close Gate]

