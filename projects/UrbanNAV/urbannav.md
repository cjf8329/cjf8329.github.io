<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>UrbanNAV — BLE Indoor Positioning System</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif;
    font-size: 16px;
    line-height: 1.7;
    color: #1f2328;
    background: #ffffff;
    padding: 2rem 1rem;
  }

  main {
    max-width: 800px;
    margin: 0 auto;
  }

  h1 {
    font-size: 2rem;
    font-weight: 600;
    border-bottom: 1px solid #d1d9e0;
    padding-bottom: 0.4rem;
    margin-bottom: 0.5rem;
  }

  h2 {
    font-size: 1.35rem;
    font-weight: 600;
    border-bottom: 1px solid #d1d9e0;
    padding-bottom: 0.3rem;
    margin: 2rem 0 0.75rem;
  }

  h3 {
    font-size: 1.05rem;
    font-weight: 600;
    margin: 1.25rem 0 0.4rem;
  }

  p { margin-bottom: 0.75rem; }

  em { font-style: italic; color: #57606a; }

  ul, ol {
    padding-left: 1.5rem;
    margin-bottom: 0.75rem;
  }

  li { margin-bottom: 0.2rem; }

  hr {
    border: none;
    border-top: 1px solid #d1d9e0;
    margin: 2rem 0;
  }

  code {
    font-family: "SFMono-Regular", Consolas, "Liberation Mono", Menlo, monospace;
    font-size: 85%;
    background: #f6f8fa;
    border: 1px solid #d1d9e0;
    border-radius: 4px;
    padding: 0.1em 0.4em;
  }

  pre {
    background: #f6f8fa;
    border: 1px solid #d1d9e0;
    border-radius: 6px;
    padding: 1rem;
    overflow-x: auto;
    margin: 1rem 0;
    font-family: "SFMono-Regular", Consolas, "Liberation Mono", Menlo, monospace;
    font-size: 13px;
    line-height: 1.6;
  }

  pre code {
    background: none;
    border: none;
    padding: 0;
    font-size: inherit;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    margin: 1rem 0;
    font-size: 15px;
  }

  th {
    text-align: left;
    padding: 0.5rem 0.75rem;
    background: #f6f8fa;
    border: 1px solid #d1d9e0;
    font-weight: 600;
  }

  td {
    padding: 0.5rem 0.75rem;
    border: 1px solid #d1d9e0;
    vertical-align: top;
  }

  tr:nth-child(even) td { background: #f6f8fa; }

  .badges { margin: 0.75rem 0 1rem; display: flex; flex-wrap: wrap; gap: 0.4rem; }
  .badge {
    display: inline-block;
    font-size: 12px;
    font-weight: 500;
    padding: 3px 10px;
    border-radius: 20px;
    border: 1px solid #d1d9e0;
    background: #f6f8fa;
    color: #57606a;
  }

  .block-diagram {
    background: #f6f8fa;
    border: 1px solid #d1d9e0;
    border-radius: 6px;
    padding: 1.25rem;
    font-family: "SFMono-Regular", Consolas, monospace;
    font-size: 13px;
    line-height: 1.8;
    margin: 1rem 0;
    white-space: pre;
    overflow-x: auto;
  }

  a { color: #0969da; text-decoration: none; }
  a:hover { text-decoration: underline; }

  footer {
    margin-top: 3rem;
    padding-top: 1rem;
    border-top: 1px solid #d1d9e0;
    font-size: 14px;
    color: #57606a;
  }
</style>
</head>
<body>
<main>

<h1>UrbanNAV — BLE Indoor Positioning System</h1>
<em>A BLE beacon-based indoor trilateration system using nRF52840 boards running Zephyr RTOS, paired with an Android/Kotlin positioning app.</em>

<div class="badges">
  <span class="badge">nRF52840</span>
  <span class="badge">Zephyr RTOS</span>
  <span class="badge">BLE / GATT</span>
  <span class="badge">Embedded C</span>
  <span class="badge">Android / Kotlin</span>
  <span class="badge">Trilateration</span>
</div>

<hr>

<h2>Overview</h2>

<p>UrbanNAV estimates a device's 2D position inside a building using signal strength from three or more BLE beacons. Each beacon is an nRF52840 Feather board running Zephyr RTOS, advertising a known identifier. The Android app collects RSSI readings, converts them to distance estimates using a calibrated path loss model, and runs a trilateration solver to output a live position fix.</p>

<p>The system requires no infrastructure changes — no Wi-Fi fingerprinting database, no dedicated hardware. Just beacons placed at known coordinates.</p>

<h2>System Overview</h2>

<h3>Block Diagram</h3>

<div class="block-diagram">┌─────────────┐   ┌─────────────┐   ┌─────────────┐
│  Beacon A   │   │  Beacon B   │   │  Beacon C   │
│ nRF52840    │   │ nRF52840    │   │ nRF52840    │
│ Zephyr RTOS │   │ Zephyr RTOS │   │ Zephyr RTOS │
└──────┬──────┘   └──────┬──────┘   └──────┬──────┘
       │                 │                 │
       └──────── BLE advertisements ───────┘
                         │  (RSSI)
                         ▼
              ┌──────────────────────┐
              │     Android App      │
              │  BLE Scanner (Kotlin)│
              │  RSSI → Distance     │
              │  Trilateration       │
              └──────────┬───────────┘
                         │  GATT (runtime config)
                         ▼
                  Position Fix (x, y)</div>

<h2>Core Components</h2>

<table>
  <thead><tr><th>Component</th><th>Details</th></tr></thead>
  <tbody>
    <tr><td>Microcontroller</td><td>nRF52840 (Adafruit Feather)</td></tr>
    <tr><td>RTOS</td><td>Zephyr RTOS</td></tr>
    <tr><td>Radio</td><td>BLE 5.0</td></tr>
    <tr><td>Firmware Language</td><td>Embedded C</td></tr>
    <tr><td>Mobile App</td><td>Android / Kotlin</td></tr>
    <tr><td>Path Loss Exponent</td><td>n = 1.74 (calibrated on-site)</td></tr>
    <tr><td>Minimum Beacons</td><td>3 (for 2D trilateration)</td></tr>
  </tbody>
</table>

<h2>How It Works</h2>

<h3>Firmware (Embedded C on Zephyr)</h3>
<ul>
  <li>Each beacon advertises a unique ID and TX power in the BLE payload</li>
  <li>Custom GATT service exposes TX power, advertising interval, and beacon ID as writable characteristics — no reflash needed to reconfigure</li>
  <li>Config is persisted to flash via Zephyr NVS and survives power cycles</li>
  <li>Static public BLE address assigned per board to prevent MAC randomization from breaking beacon identification</li>
</ul>

<pre><code>/* GATT write handler — update TX power at runtime */
static ssize_t write_tx_power(struct bt_conn *conn,
    const struct bt_gatt_attr *attr,
    const void *buf, uint16_t len,
    uint16_t offset, uint8_t flags)
{
    if (len != sizeof(int8_t)) {
        return BT_GATT_ERR(BT_ATT_ERR_INVALID_ATTRIBUTE_LEN);
    }
    int8_t new_power = *((int8_t *)buf);
    bt_le_set_tx_power(new_power, 0, 0);
    nvs_write(&fs, TX_POWER_ID, &new_power, sizeof(new_power));
    return len;
}</code></pre>

<h3>Android App (Kotlin)</h3>
<ul>
  <li>Scans for BLE advertisements and identifies beacons by MAC address</li>
  <li>Converts RSSI to distance using the log-distance path loss model with calibrated n = 1.74</li>
  <li>Runs a least-squares trilateration solver over 3+ beacon measurements</li>
  <li>GATT client lets the user write config parameters to any beacon from the app UI</li>
</ul>

<pre><code>// RSSI → distance using calibrated path loss exponent
fun rssiToDistance(rssi: Int, txPower: Int): Double {
    val n = 1.74   // measured on-site
    return 10.0.pow((txPower - rssi) / (10.0 * n))
}

// Trilateration: solve 2D position from 3+ beacons
fun trilaterate(beacons: List&lt;BeaconMeasurement&gt;): PointF {
    val A = buildMatrix(beacons)   // least-squares system A·x = b
    val b = buildVector(beacons)
    return leastSquaresSolve(A, b)
}</code></pre>

<hr>

<h2>Key Challenges &amp; Solutions</h2>

<table>
  <thead><tr><th>Challenge</th><th>Solution</th></tr></thead>
  <tbody>
    <tr>
      <td>Beacons dropping advertisements intermittently</td>
      <td>Traced to outdated nice!nano bootloader incompatible with Zephyr's BLE stack — flashing the correct bootloader resolved the issue</td>
    </tr>
    <tr>
      <td>RSSI too noisy for reliable distance estimates</td>
      <td>Rolling average over scan window; separate n calibration per environment</td>
    </tr>
    <tr>
      <td>Path loss exponent unknown for deployment space</td>
      <td>On-site calibration at known distances; measured n = 1.74 (lower than free-space n = 2 due to indoor reflections)</td>
    </tr>
    <tr>
      <td>Runtime reconfiguration required reflashing</td>
      <td>Implemented GATT service exposing TX power, interval, and beacon ID as writable characteristics</td>
    </tr>
    <tr>
      <td>MAC address randomization breaking beacon lookup</td>
      <td>Disabled randomization in Zephyr config; assigned static public address per board</td>
    </tr>
  </tbody>
</table>

<hr>

<h2>Results</h2>

<ul>
  <li>End-to-end system functional: beacons advertising stably, app resolving live position</li>
  <li>GATT config working from app UI — TX power, interval, and beacon ID writable without reflash</li>
  <li>Calibrated path loss exponent (n = 1.74) outperforms free-space default (n = 2) in indoor environments</li>
  <li>Live position updates from a minimum of 3 beacons</li>
</ul>

<h2>Full Stack</h2>

<table>
  <thead><tr><th>Layer</th><th>Technology</th><th>Notes</th></tr></thead>
  <tbody>
    <tr><td>MCU</td><td>nRF52840</td><td>ARM Cortex-M4F, integrated BLE 5.0</td></tr>
    <tr><td>RTOS</td><td>Zephyr RTOS</td><td>BLE host stack, NVS, device tree</td></tr>
    <tr><td>Firmware</td><td>Embedded C</td><td>GATT service, advertiser, NVS config</td></tr>
    <tr><td>Mobile</td><td>Android / Kotlin</td><td>BluetoothLeScanner, GATT client</td></tr>
    <tr><td>Algorithm</td><td>Trilateration</td><td>Least-squares, calibrated n = 1.74</td></tr>
    <tr><td>Radio</td><td>BLE 5.0</td><td>Static public address per beacon</td></tr>
    <tr><td>Persistence</td><td>Zephyr NVS</td><td>Config survives power cycle</td></tr>
  </tbody>
</table>

<h2>Future Improvements</h2>

<ul>
  <li>Kalman filter on position estimates to smooth noisy fixes</li>
  <li>Weighted trilateration by signal quality</li>
  <li>Fingerprinting mode as alternative to trilateration</li>
  <li>Visual floor-plan overlay in the Android app</li>
  <li>Auto-calibration mode for path loss exponent</li>
  <li>UWB upgrade for sub-meter accuracy</li>
  <li>Custom PCB for beacon hardware</li>
</ul>

<hr>

<footer>
  Christopher Fonseca &nbsp;·&nbsp; NYU Tandon &nbsp;·&nbsp; <a href="https://cjf8329.github.io">cjf8329.github.io</a>
</footer>

</main>
</body>
</html>
