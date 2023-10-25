# web server 

# Web Server Update

We've revamped our web server and introduced the new ViperAPI! With the ViperAPI, users can seamlessly integrate the capabilities of ViperVision into their applications. This web server serves as a showcase for the ViperAPI's capabilities.

**Key Features**:
- **gRPC**: An open-source RPC framework. [Learn more](https://grpc.io/).
- **Language Agnostic**: Can be implemented in multiple programming languages without compatibility issues.
- **Http/2 over Http/1**: Offers better performance and lower latency.
- **Single Camera Page**: Can now open each camera individually.

# Viper API Documentation

The Viper API provides functionalities to interact with camera devices.

## Methods

### `FetchAvailableCameras(query: string) -> cameras: map`

Fetch the list of available cameras.

- **query**: A filter string for available cameras (optional).
  
**Returns:** 
- A map containing the camera IDs as keys and their information as values.

---

### `StreamCameraImages(cameraId: string) -> stream<CameraImage>`

Starts a stream of images from the specified camera.

- **cameraId**: Identifier of the camera from which images will be streamed.

**Returns (streamed):**
- **cameraId**: Identifier of the source camera.
- **image**: Image data in bytes.
- **sequenceNumber**: Sequence number of the image in the stream.
- **timestamp**: Capture timestamp of the image.
- **format**: Image format (e.g., JPEG, PNG).

---

### `Snapshot(cameraIds: array<string>) -> snapshots: array<CameraImage>`

Takes a snapshot of the specified cameras.

- **cameraIds**: List of camera IDs (optional; if empty, all cameras are considered).

**Returns:** 
- List of images taken during the snapshot.

---

### `Recording(cameraIds: array<string>, start: bool) -> success: bool, message: string`

Start or stop recording for the specified cameras.

- **cameraIds**: List of camera IDs (optional; if empty, affects all cameras).
- **start**: Flag to start (`true`) or stop (`false`) recording.

**Returns:** 
- **success**: Indicates the success of the operation.
- **message**: A message indicating the outcome (especially useful for errors).

---

### `GenerateReport(cameraIds: array<string>) -> reportLink: string`

Generate a report for the specified cameras.

- **cameraIds**: List of camera IDs (optional; if empty, a report for all cameras is generated).

**Returns:** 
- **reportLink**: URL or path to the generated report.

---

### `GetApplicationTheme() -> theme: string`

Retrieve the current theme of the application.

**Returns:** 
- The name of the current theme.


### Code Examples

- Fetch camera information:

```cpp
#include <iostream>
#include <memory>
#include <string>

#include <grpcpp/grpcpp.h>
#include "viper_api.grpc.pb.h"

class ViperClient {
public:
    ViperClient(std::shared_ptr<grpc::Channel> channel)
        : stub_(viper_api::CameraStreamingService::NewStub(channel)) {}

    void FetchCameras() {
        viper_api::AvailableCamerasRequest request;
        request.set_query(""); // Empty for this example
        viper_api::AvailableCamerasResponse response;

        grpc::ClientContext context;
        grpc::Status status = stub_->FetchAvailableCameras(&context, request, &response);

        if (status.ok()) {
            std::cout << "Successfully fetched available cameras!" << std::endl;
            // Handle the response, e.g., print camera information.
        } else {
            std::cout << "Failed to fetch cameras with error: " << status.error_message() << std::endl;
        }
    }

private:
    std::unique_ptr<viper_api::CameraStreamingService::Stub> stub_;
};

int main(int argc, char** argv) {
    ViperClient client(grpc::CreateChannel("localhost:50051", grpc::InsecureChannelCredentials()));
    client.FetchCameras();
    return 0;
}

```

- Getting an image:

```cpp
#include <iostream>
#include <memory>
#include <string>

#include <grpcpp/grpcpp.h>
#include "viper_api.grpc.pb.h"

class ViperClient {
public:
    ViperClient(std::shared_ptr<grpc::Channel> channel)
        : stub_(viper_api::CameraStreamingService::NewStub(channel)) {}

    void StreamImagesFromCamera(const std::string& cameraId) {
        viper_api::CameraImageStreamRequest request;
        request.set_cameraid(cameraId);

        grpc::ClientContext context;

        auto reader = stub_->StreamCameraImages(&context, request);
        viper_api::CameraImage image;
        while (reader->Read(&image)) {
            // Process the streamed image data
            std::cout << "Received image with sequence number: " << image.sequencenumber() 
                      << " and format: " << image.format() << std::endl;
            // You can also extract and process the image bytes here
        }

        grpc::Status status = reader->Finish();
        if (!status.ok()) {
            std::cerr << "StreamCameraImages RPC failed with status: " << status.error_message() << std::endl;
        }
    }

private:
    std::unique_ptr<viper_api::CameraStreamingService::Stub> stub_;
};

int main(int argc, char** argv) {
    ViperClient client(grpc::CreateChannel("localhost:31777", grpc::InsecureChannelCredentials()));
    client.StreamImagesFromCamera("some_camera_id");
    return 0;
}

```