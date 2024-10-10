# ParaSeed-Agent System Installation, Introduction, and User Guide 🌱🤖

[<中文版本>](Chinese_README.md)  <英文版本>

## Introduction 📖

ParaSeed-Agent is a multifunctional intelligent agent system designed specifically for the agricultural sector. By integrating various artificial intelligence models and tools, it aims to intelligently analyze and respond to user inputs. It can handle multiple types of tasks, including:

- **General Text Q&A** 📝
- **Multimodal Processing (e.g., Image Analysis)** 🖼️
- **Crop Growth Monitoring** 🌾
- **Database Querying and Storage** 🗄️
- **Sensor Parameter Acquisition, Setting, and Control** 📡

## Features ✨

- **Multi-task Processing**: Automatically identifies the type of task from user input and invokes the corresponding execution agent for processing.
- **MQTT Sensor Control**: Implements remote monitoring and control of sensors via the MQTT protocol.
- **Multimodal Processing**: Integrates multimodal models (like ParaSeed-Multi) to analyze and process images, providing professional agricultural image analysis.
- **Database Querying and Storage**: Supports intelligent querying and processing of databases, and can store sensor data in real-time to remote databases.
- **Crop Growth Monitoring**: Controls camera positions to capture real-time images of crops and analyze their growth status.

## System Requirements 🖥️

- **Operating System**: Supports Windows, macOS, and Linux
- **Python Version**: 3.6 or above
- **Dependencies**:

  - `openai`
  - `torch`
  - `transformers`
  - `Pillow` (PIL)
  - `peft`
  - `opencv-python` (cv2)
  - `pandas`
  - `pymysql`
  - `paho-mqtt`
  - `langchain`
  - `langchain_experimental`

## Installation Guide 🚀

### 1. Clone or Download the Code 📥

```bash
git clone https://github.com/your-repo/ParaSeed-Agent.git
```

### 2. Install Dependencies 📦

In the project directory, run the following command to install the required Python libraries:

```bash
pip install -r requirements.txt
```

*If there is no `requirements.txt` file, you can manually install the required libraries:*

```bash
pip install openai torch transformers Pillow peft opencv-python pandas pymysql paho-mqtt langchain langchain_experimental
```

### 3. Configure Environment Variables 🔧

In the code, configure the OpenAI API key and base URL:

```python
import os
os.environ["OPENAI_API_KEY"] = "your OpenAI API key"
os.environ["OPENAI_BASE_URL"] = "https://api.openai.com/v1"
```

**Note**: Ensure that you store and use your API key securely; do not expose it in your code repository.

## User Guide 📚

### 1. Launch the Program ▶️

Run the main program from the command line:

```bash
python main.py
```

The program will prompt you to enter a question:

```bash
请输入您的问题：
```

*(Translation: "Please enter your question:")*

### 2. Enter Your Question 💬

You can input the following types of questions:

- **General Text Questions**: For example, "How's the weather today?" ☀️
- **Multimodal Questions**: Questions involving image analysis; the system will automatically capture images or you can provide an image path. 🖼️
- **Crop Growth Monitoring**: For example, "Please analyze the growth status of Crop No.2." 🌱
- **Database Queries**: For example, "Please check the temperature and humidity in the greenhouse on August 27, 2023." 🌡️💧
- **Sensor Control**: For example, "Set the sensor temperature to 25 degrees Celsius." 🔥

### 3. System Response 📝

The system will automatically identify the task type based on your input and invoke the corresponding agent for processing. After processing, it will output the result.

## Main Module Overview 🧩

ParaSeed-Agent consists of multiple modules, each responsible for specific functionalities. Below is a detailed introduction to the main modules, especially focusing on remote database integration, storage and querying, and the actual operational flow of crop growth monitoring.

### 1. Main Program Logic (`main` Function) 🧠

**Functionality**: Manages the overall process flow, including receiving user input, calling the question analysis agent, assigning tasks to the corresponding execution agents, summarizing results, and outputting them.

**Process**:

1. **Receive User Input**: Gets the user's question and (optional) image data.
2. **Question Analysis**: Calls `QuestionAnalysisAgent` to analyze user input and determine the task type.
3. **Task Assignment and Execution**: Instantiates the corresponding execution agent based on the task type and calls its `execute` method.
4. **Result Summarization**: Uses `SummaryAgent` to aggregate the results from various execution agents to form the final answer.

### 2. Question Analysis Agent (`QuestionAnalysisAgent`) 🧐

**Functionality**: Uses the GPT model to analyze user input and determine the task type.

**Task Types**:

- `gpt`: General text questions
- `multimodal`: Multimodal processing (e.g., image analysis)
- `crop_growth`: Crop growth monitoring
- `database`: Database querying
- `sensor_control`: Sensor parameter acquisition, setting, and control

**Main Methods**:

- `analyze(extracted_user_input)`: Receives user input and uses predefined prompt templates to call the GPT model, returning the task type.

### 3. MQTT Sensor Control Agent (`MQTTSensorControlAgent`) 📡

**Functionality**: Communicates with sensor devices via the MQTT protocol to achieve parameter acquisition and setting.

**Main Methods**:

- `get_sensor_value()`: Subscribes to the sensor topic to obtain the current sensor value.
- `set_control_value(control_param)`: Publishes messages to the control topic to set sensor parameters.
- `execute(user_input)`: Parses user input to determine whether to perform a query or setting operation and executes the corresponding method.

### 4. Multimodal Processing Agent (`ParaSeedMultiAgent`) 🖼️

**Functionality**: Calls a multimodal model (like ParaSeed-Multi) to jointly analyze images and text, providing professional agricultural image interpretations.

**Main Methods**:

- `call_paraseed_multi(image_paths, question, history)`: Interacts with the multimodal model by sending images and questions to obtain analysis results.
- `capture_image()`: Captures real-time images via a camera or video stream for analysis.
- `execute(user_input)`: Receives user input, captures images, calls the multimodal model for processing, and returns the result.

### 5. Crop Growth Execution Agent (`CropGrowthExecutionAgent`) 🌾

**Functionality**: Controls the camera to move to the corresponding position based on the specified crop number, captures crop images, and analyzes their growth status.

**Main Methods**:

- `update_topics(deviceID)`: Dynamically updates MQTT topics based on the device ID.
- `get_current_position_from_sensor()`: Obtains the current position of the sensor.
- `set_control_value(deviceID, control_param)`: Sets the device's control parameters to move the camera to the target position.
- `capture_image()`: Captures images at the current camera position.
- `call_gpt4_vision(image_path, question)`: Calls the GPT-4 Vision model to analyze images.
- `execute(user_input)`: Integrates the above methods to complete the crop growth analysis task.

**Detailed Explanation**:

- **Position Judgment and Adjustment**: When the user inquires about the growth status of a crop, the system first obtains the current position of the camera via `get_current_position_from_sensor()`. If the camera is not at the target crop's position, it controls the slider to move to the corresponding crop position using `set_control_value()`.
- **Image Capture and Analysis**: After the camera reaches the target position, it captures real-time images of the crop using `capture_image()`. Then, it analyzes the image using `call_gpt4_vision()` or `call_paraseed_multi()`, determines the crop's growth status, and provides a conclusion.

### 6. Database Query Agent (`DatabaseQueryAgent`) 🗄️

**Functionality**: Parses the user's database query request, generates SQL statements, executes the query, and returns the result.

**Main Methods**:

- `_parse_database_info(user_input)`: Uses the GPT model to parse the database name, table name, and SQL query statement from the user's input.
- `execute(user_input)`: Executes the database query and generates an appropriate response based on the result.

**Detailed Explanation**:

- **Query Result Processing**:
  - **When Data Exists**: If the database contains the corresponding data, the system retrieves the query result, organizes it, and returns it to the user.
  - **When Data Does Not Exist**: If there's no relevant data in the database, the system initiates the sensor for real-time measurement, stores the measured data to the remote database, and informs the user of the current information.

### 7. Sensor Monitoring Agent (`SensorMonitorAgent`) 📈

**Functionality**: Monitors sensor data in real-time, saves it to a remote database, and provides the latest sensor status based on user requests.

**Main Methods**:

- `on_connect(client, userdata, flags, rc)`: MQTT connection callback function that subscribes to the sensor topic.
- `on_message(client, userdata, msg)`: MQTT message callback function that processes received sensor data.
- `process_sensor_data(sensor_data)`: Processes sensor data and generates SQL insert statements.
- `generate_sql_insert(sensor_data)`: Uses the GPT model to generate SQL statements to insert data into the database.
- `insert_data_to_database(sensor_data)`: Executes the SQL insert operation to save data to the database.
- `execute(user_input)`: Waits for sensor data reception to complete, then generates a response based on user input and sensor data.

**Detailed Explanation**:

- **Data Storage and Query Process**:
  - When the user queries information in the greenhouse for a specific time, the system first attempts to retrieve data from the database.
  - **If Data Exists**: Directly returns the query result, organizes it, and provides it to the user.
  - **If Data Does Not Exist**: The system initiates the sensor for real-time measurement to obtain the latest data.
    - Stores the newly acquired data to the remote database to ensure continuous data updates.
    - Returns the current measurement result to the user, informing them of the latest information.

### 8. GPT Execution Agent (`GPTExecutionAgent`) 📝

**Functionality**: Handles general text questions by calling the GPT model to generate responses.

**Main Methods**:

- `execute(user_input)`: Directly calls the GPT model to generate an answer to the user's question.

### 9. Summary Agent (`SummaryAgent`) 📊

**Functionality**: Aggregates results from various execution agents to form a complete answer.

**Main Methods**:

- `summarize(results)`: Receives a list of results and aggregates them.

## Important Notes ⚠️

- **API Key Security** 🔐: Ensure your API keys are stored securely; do not expose them directly in your code.
- **MQTT Configuration** 🌐: Before using MQTT functionalities, make sure to correctly configure the MQTT server's address, port, username, and password.
- **Database Configuration** 💾: Before using database functionalities, ensure the database connection information is correct, and the required database and tables have been created.
- **Error Handling** ❗: In actual deployment, it is recommended to add exception handling to each module to ensure the robustness of the system.

## Examples 📖

### Example 1: Querying Greenhouse Temperature and Humidity 🌡️💧

**User Input**:

```bash
请输入您的问题：请帮我从 greenhouse_data 表中查询一下2023年8月27日的温度和湿度。
```

*(Translation: "Please help me query the temperature and humidity from the greenhouse_data table on August 27, 2023.")*

**System Processing Flow**:

1. **Question Analysis**: `QuestionAnalysisAgent` identifies the task type as `database`.
2. **Execution Agent**: `DatabaseQueryAgent` parses the user input and generates an SQL query.
3. **Database Query**:
   - **If Data Exists**:
     - The system finds the corresponding data for the date in the database.
     - Organizes the query result and returns it to the user.
     - **System Response**:
       ```
       2023年8月27日的大棚温度为25摄氏度，湿度为60%。
       ```
       *(Translation: "The greenhouse temperature on August 27, 2023, was 25°C, and the humidity was 60%.")*
   - **If Data Does Not Exist**:
     - The system does not find corresponding data in the database.
     - **Initiates Sensor Measurement**:
       - Starts `SensorMonitorAgent` to activate sensors for real-time measurement.
       - Stores the measured data to the remote database.
     - **Returns Result**:
       ```
       数据库中未找到2023年8月27日的数据。已进行实时测量，当前温度为26摄氏度，湿度为58%。
       ```
       *(Translation: "No data found for August 27, 2023, in the database. A real-time measurement has been performed; the current temperature is 26°C, and the humidity is 58%.")*

### Example 2: Querying Crop Growth Status 🌱

**User Input**:

```bash
请输入您的问题：请分析一下2号作物的当前生长状况。
```

*(Translation: "Please analyze the current growth status of Crop No.2.")*

**System Processing Flow**:

1. **Question Analysis**: `QuestionAnalysisAgent` identifies the task type as `crop_growth`.
2. **Execution Agent**: `CropGrowthExecutionAgent` handles this task.
3. **Position Judgment and Adjustment**:
   - The system obtains the current position of the camera via `get_current_position_from_sensor()`.
   - Determines whether the camera is at Crop No.2's position.
     - **If at Target Position**:
       - Proceeds directly with image capture and analysis.
     - **If Not at Target Position**:
       - Controls the slider to move the camera to Crop No.2's position using `set_control_value()`.
       - Waits for the movement to complete before proceeding.
4. **Image Capture and Analysis**:
   - Uses `capture_image()` to obtain a real-time image of Crop No.2.
   - Calls `ParaSeedMultiAgent` and uses the ParaSeed-Multi multimodal model to analyze the crop's growth status.
5. **Returns Result**:
   - Organizes the analysis result and provides it to the user.
   - **System Response**:
     ```
     2号作物目前生长良好，叶片颜色正常，无明显病虫害迹象。
     ```
     *(Translation: "Crop No.2 is currently growing well, with normal leaf coloration and no obvious signs of pests or diseases.")*

### Example 3: Sensor Parameter Control 🔧

**User Input**:

```bash
请输入您的问题：将传感器的温度设置为22摄氏度。
```

*(Translation: "Set the sensor temperature to 22 degrees Celsius.")*

**System Processing Flow**:

1. **Question Analysis**: `QuestionAnalysisAgent` identifies the task type as `sensor_control`.
2. **Execution Agent**: `MQTTSensorControlAgent` parses the user input to extract the parameter to be set.
3. **Execute Control**:
   - Publishes a command to the control topic via MQTT to set the temperature.
   - Waits for control feedback to confirm the command was executed successfully.
4. **Returns Result**:
   - **System Response**:
     ```
     已将传感器温度设置为22摄氏度。
     ```
     *(Translation: "The sensor temperature has been set to 22°C.")*

### Example 4: Automatic Measurement When Data is Absent in the Database 📊

**User Input**:

```bash
请输入您的问题：请帮我查询一下今天的大棚湿度是多少。
```

*(Translation: "Please help me check today's greenhouse humidity.")*

**System Processing Flow**:

1. **Question Analysis**: `QuestionAnalysisAgent` identifies the task type as `database`.
2. **Execution Agent**: `DatabaseQueryAgent` parses the user input and generates an SQL query.
3. **Database Query**:
   - **If Data Does Not Exist** (no data for today):
     - The system does not find data for today in the database.
     - **Automatic Measurement**:
       - Starts `SensorMonitorAgent` to activate sensors for real-time humidity measurement.
       - Stores the measurement result to the remote database.
     - **Returns Result**:
       ```
       数据库中未找到今天的湿度数据。已进行实时测量，当前湿度为62%。
       ```
       *(Translation: "No humidity data found for today in the database. A real-time measurement has been performed; the current humidity is 62%.")*

### Example 5: Camera Position Adjustment and Crop Analysis 📷

**User Input**:

```bash
请输入您的问题：请查看一下1号作物的生长情况，有没有出现病虫害？
```

*(Translation: "Please check the growth status of Crop No.1; are there any pests or diseases?")*

**System Processing Flow**:

1. **Question Analysis**: `QuestionAnalysisAgent` identifies the task type as `crop_growth`.
2. **Execution Agent**: `CropGrowthExecutionAgent` handles this task.
3. **Position Judgment and Adjustment**:
   - Obtains the current position of the camera (e.g., at Crop No.2).
   - Determines that the camera is not at Crop No.1's position.
   - **Moves Camera**:
     - Controls the slider to move the camera to Crop No.1's position using `set_control_value()`.
     - Waits for the movement to complete, confirming the camera has reached the target position.
4. **Image Capture and Analysis**:
   - Uses `capture_image()` to obtain a real-time image of Crop No.1.
   - Calls `ParaSeedMultiAgent` and uses the ParaSeed-Multi multimodal model to analyze the crop for pests or diseases.
5. **Returns Result**:
   - **System Response**:
     ```
     已查看1号作物。当前生长状况良好，未发现明显病虫害迹象。叶片健康，颜色正常。建议继续保持定期检查。
     ```
     *(Translation: "Crop No.1 has been checked. It is currently growing well with no obvious signs of pests or diseases. Leaves are healthy and coloration is normal. It is recommended to continue regular inspections.")*

## FAQs ❓

### 1. How Do I Add New Crop Device Information? 🌿

You can add new crop information in the `crop_device_info` dictionary in the code, for example:

```python
crop_device_info = {
    "Crop No.1": {"deviceID": "modbus", "weizhi": 20},
    "Crop No.2": {"deviceID": "modbus2", "weizhi": 25},
    # Add more crops
    "Crop No.3": {"deviceID": "modbus3", "weizhi": 30},
}
```

### 2. How Do I Change MQTT or Database Configurations? ⚙️

Modify the MQTT and database configuration parameters during the initialization of the corresponding agent classes, for example:

```python
mqtt_broker = "your MQTT Broker IP"
mqtt_port = 1883
mqtt_username = "your MQTT username"
mqtt_password = "your MQTT password"

db_host = "your database host"
db_port = 3306
db_user = "your database username"
db_password = "your database password"
db_name = "your database name"
```

### 3. How Do I Securely Store Sensitive Information? 🔒

It is recommended to use environment variables or configuration files to store sensitive information such as API keys, usernames, and passwords. Avoid writing this information directly into your code.

## Conclusion 🎉

ParaSeed-Agent provides an intelligent agent system that integrates multiple functionalities, aiming to offer convenient and efficient solutions for applications in the agricultural sector. By properly configuring and using the system, you can achieve remote control of sensors, crop growth monitoring, database management, and other functionalities.
