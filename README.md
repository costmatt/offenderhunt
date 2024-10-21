# offenderhunt
The following lines of code is integrating a Bash script that interacts with the San Antonio Police Department API for sex offender data and utilizes a Python facial recognition program, The end result and goal is to create a deployment on Ray-Ban META Smart Glasses, which involves several layers (yet to be implemented in this growing project).

### Prerequisites

1. **Ray-Ban META Smart Glasses**: Ensure that you have access to the development environment for the glasses.
2. **Python and Required Libraries**: Make sure you have Python installed on the glasses, along with libraries such as `requests`, `face_recognition`, and any necessary libraries for image handling.
3. **API Key**: Obtain an API key if required by the San Antonio Police Department API.

### Bash Script (`offenderhunt.sh`)

This script fetches the sex offender data and processes it using a Python script.

```bash
#!/bin/bash

# Set the API URL
API_URL="https://api.sanantonio.gov/sex-offenders"  # Replace with the actual endpoint
OUTPUT_FILE="offenders.json"

# Fetch the data from the API
echo "Fetching sex offender data..."
curl -s "$API_URL" -o "$OUTPUT_FILE"

# Check if the data was fetched successfully
if [[ $? -ne 0 ]]; then
    echo "Failed to fetch data from API."
    exit 1
fi

# Process the data with the Python script
echo "Processing data with Python..."
python3 scan_off.py "$OUTPUT_FILE"
```

### Python Script (`scan_off.py`)

This Python script handles the facial recognition part.
```
import sys
import json
import face_recognition
from PIL import Image
import requests
import io

def load_offenders(file_path):
    with open(file_path, 'r') as file:
        return json.load(file)

def recognize_faces(offenders):
    for offender in offenders['results']:  # Adjust based on actual JSON structure
        image_url = offender['photo_url']  # Adjust key as per API response
        response = requests.get(image_url)

        if response.status_code == 200:
            image = face_recognition.load_image_file(io.BytesIO(response.content))
            face_locations = face_recognition.face_locations(image)

            if face_locations:
                print(f"Found {len(face_locations)} face(s) for {offender['name']}")
            else:
                print(f"No faces found for {offender['name']}")
        else:
            print(f"Failed to fetch image for {offender['name']}")

def main():
    if len(sys.argv) != 2:
        print("Usage: python scan_off.py <path_to_offenders_json>")
        sys.exit(1)

    offenders_file = sys.argv[1]
    offenders = load_offenders(offenders_file)
    recognize_faces(offenders)

if __name__ == "__main__":
    main()
```

### Usage on Ray-Ban META Smart Glasses

To run this on Ray-Ban META Smart Glasses, you would typically follow these steps:

1. **Transfer Scripts**: Ensure that both scripts are transferred to the device.
2. **Setup Environment**: Ensure that the required Python libraries are installed on the device.
3. **Make the Bash Script Executable**: Run `chmod +x offenderhunt.sh`.
4. **Run the Script**: Execute `./offenderhunt.sh` from the terminal or appropriate interface on the glasses.

### Considerations

- **User Interface**: If you are implementing this in an interactive environment on the glasses, consider how you will display information to the user, like visual alerts when a match is found.
- **Privacy Concerns**: Be mindful of privacy issues regarding the use of facial recognition and handling sensitive data like sex offender lists.
- **Performance**: Ensure that the processing load is manageable on the hardware of the glasses. You may need to optimize image processing or consider cloud processing if performance is inadequate.

This framework provides a starting point. You'll need to refine it based on specific requirements and test it thoroughly to ensure it works effectively on the device.
