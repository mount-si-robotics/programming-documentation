# Setting up the Limelight 3A
Limelight's documentation is very silly sometimes and doesn't contain some information for Limelight 3A.

## Wiring Limelight 3A for FTC
When you're ready to use your Limelight on your robot:
- Run a USBC to USBA cable from your Limelight 3A to your Control Hub's USB 3.0 Port
- Connect to your Control Hub by plugging into a USB 3.0 port (blue port)

Limelight 3A does not support Google Coral. You can still use neural detection and classification pipelines by setting the neural network runtime engine to "CPU".

## Accessing the Web Interface
- Method 1: Open the Limelight Hardware Manager application, scan for Limelights, and double-click on your Limelight 3A when it appears
- Method 2: Open a web browser and navigate to [http://limelight.local:5801](http://limelight.local:5801)
- Method 3: Use one of the static IP Addresses
    - Limelight 3A configuration UI on Windows (USB-Ethernet): [http://172.28.0.1:5801](http://172.28.0.1:5801)
    - Limelight 3A configuration UI on Mac/Linux/Android (USB-Ethernet): [http://172.29.0.1:5801](http://172.29.0.1:5801)