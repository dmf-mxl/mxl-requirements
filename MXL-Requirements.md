Draft for discussion. 

Color in the Section column
* ${\color{red}Red}$ = there is a between the user requirement and what the TSC as done or is planning to do. At the end there is a list that put more context on those differences. These items needs to be reviewed by the RC

## MVP Approach
We intent that the Reference Media Exchange SDK Reference Components be developed using a phased approach, with specific emphasis on a first phase aiming to reach a Minimal Viable Prototype (MVP).
 
The MVP approach would allow quick start-up of trial implementations, enabling vendors and end users to experiment sooner and learn about issues that could lead to optimization for future phases. Even if part of the original MVP version requires some re-work in later phases, it is better to begin experimenting and learning rather than wait for a fully featured version to be completed. 
 
Each of the requirements below is assigned to one of three phases: 
* **Phase 1:** The Minimal Viable Prototype (MVP) to get started prototyping and experimenting
* **Phase 2:** First Release are high-priority features to be added immediately after the MVP
* **Future Phases:** To be implemented based on newly identified requirements and findings from initial testing

## 1. SDK Basics

|Section|Description|Requirements|Phase|Issue#| 
|:---|:---|:---|:---|:---|
|1.1|Is an SDK|Media Exchange Layer is implemented as a shared SDK (Software Development Kit) <br> Media Functions from multiple vendors exchange media using the **same SDK**|1|[#22](https://github.com/dmf-mxl/mxl-requirements/issues/22)|
|1.2|Is open Source|All components of the Media Exchange Layer SDK are developed as a fully Open Source as per the open source definition found here:  https://opensource.org/osd.|1|[#23](https://github.com/dmf-mxl/mxl-requirements/issues/23)|
|1.3|Allows proprietary implementations|In the future, vendors may introduce and offer for sale, their own, proprietary version of the SDK for which they will provide support. <br> Vendor versions of the SDK must be API compatible with the Open Source / Reference Version. <br> The vendor versions could provide additional capabilities and features but these would have to be provided by additional API calls or parameters which add to and not replace the base function of the reference implementation.|Future|[#26](https://github.com/dmf-mxl/mxl-requirements/issues/26)|
|1.4|On Linux|All components of the Media Exchange Layer run on Linux Operating System.|1|[#24](https://github.com/dmf-mxl/mxl-requirements/issues/24)|
|1.5|On MacOS|All components of the Media Exchange Layer run on MacOS Operating System.|Future|[#25](https://github.com/dmf-mxl/mxl-requirements/issues/25)|
|${\color{red}1.6}$|On Windows|Future versions of the Media Exchange layer can be designed to run on Windows.|Future|[#27](https://github.com/dmf-mxl/mxl-requirements/issues/27)|
|1.7|Sample Media Functions|The SDK should contain a Sample Media Function which includes calls to the Media Exchange and interaction with NMOS IS-04 Registry and IS-05 Connection Management.|1|[#28](https://github.com/dmf-mxl/mxl-requirements/issues/28)|
|1.8|Test Tools|The SDK should contain a Test Tool to test the implementation of a Media Functions calls to the Media Exchange's APIs|1|[#29](https://github.com/dmf-mxl/mxl-requirements/issues/29)|

## 2. Media Exchange via Shared Memory

|Section|Description|Requirements|Phase|Issue#|
|:---|:---|:---|:---|:---|
|${\color{red}2.1}$|Direct sharing of memory|Media exchange among functions is achieved by direct sharing of memory. (e.g., DMA / RDMA) <br> The memory exchanged can be between CPU, GPU, NPU memory, depending on the media function. <br> To minimize resource utilization, the sharing of memory is achieved without copying data from one CPU memory (Zero-Copy).|1|[#31](https://github.com/dmf-mxl/mxl-requirements/issues/31)|
|2.2|Asynchronous media exchange|Media exchange among functions is Asynchronous. The function that generates a media essence writes its data into a memory buffer as the data is being generated. <br> Receiving Functions can read the data from the memory buffer as soon as media data is written. <br> This requirement allows the exchange of media with minimal latency.|1|[#32](https://github.com/dmf-mxl/mxl-requirements/issues/32)|
|2.3|Using buffer per flow|Compute Hosts have enough shared memory to buffer multiple frames of video, samples of audio and blocks of Timed Metadata, allowing functions with different processing times to exchange media, and allowing media essences to be time-aligned if and when needed.|1|[#33](https://github.com/dmf-mxl/mxl-requirements/issues/33)|
|2.4|1 second buffer time|The size of the ring buffers is equivalent to 1 second of time (25 or 30 frames of video)|1|[#34](https://github.com/dmf-mxl/mxl-requirements/issues/34)|
|2.5|Configurable buffer time|If found useful, the size of the ring buffer is a configurable parameter of the system and can be as small or as large as the underlying hardware will support|Future|[#35](https://github.com/dmf-mxl/mxl-requirements/issues/35)|
|2.6|Memory sharing abstraction|The Media Exchange layer supports different methods for sharing memory within a Host or between Hosts in a cluster. <br> The Media Exchange layer abstracts the details of the different methods so that functions using the media exchange layer are unaware of how media is being shared.|2|[#36](https://github.com/dmf-mxl/mxl-requirements/issues/36)|
|2.7|Intra-Host memory sharing|Memory buffers containing the media are shared between functions on the same Compute Host using Zero Copy Direct Memory Access (DMA) to exchange media without taxing CPU and other Host resources.|1|[#37](https://github.com/dmf-mxl/mxl-requirements/issues/37)|
|2.8|Inter-Host memory sharing|Memory buffers containing the media are shared between functions on different Compute Hosts in the same cluster using Remote Direct Memory Access (RDMA). <br> RDMA can be achieve via different methods depending on the fabric, such as RoCEv2, UCX, Open-UCX, AWS EFA, TCP/UDP, UltraEthernet, or any other fabric with an adaptor to this RDMA abstraction.|2|[#38](https://github.com/dmf-mxl/mxl-requirements/issues/38)|
|2.9|Inter-Host memory sharing: low bitrate|For lower bit rate applications such as Audio Only, we need economical methods supported by affordable networking equipment.|2|[#39](https://github.com/dmf-mxl/mxl-requirements/issues/39)|
|2.10|Container compatibility|Media Exchange SDK components are compatible with common container platforms such as Docker, Docker Compose, Kubernetes, AKS, EKS, OpenShift, etc.|1|[#40](https://github.com/dmf-mxl/mxl-requirements/issues/40)|
|2.11|Memory isolation from kernel|The Media Exchange SDK shall allow for shared memory isolation between the memory used by Media Functions and the memory used by the kernel of the host.|2|[#41](https://github.com/dmf-mxl/mxl-requirements/issues/41)|
|2.12|Memory isolation between segments of Media Functions|The Media Exchange SDK shall allow for multiple shared memory spaces to segment groups of Media Functions that need to be kept separated. <br> These multiple shared memory spaces would be isolated from one another and the host to maintain the integrity of the system if one memory space has a critical failure.|2|[#42](https://github.com/dmf-mxl/mxl-requirements/issues/42)|

## 3. Media Buffer Data Model

### 3.1 General grain Requirements
|Section|Description|Requirements|Phase|Issue#|
|:---|:---|:---|:---|:---|
|3.1.1|Separate essences|Real-time Video, Audio and Timed Metadata (e.g. timecode, caption data, SCTE Triggers) shall be exchanged as individual data essences.|1|[#44](https://github.com/dmf-mxl/mxl-requirements/issues/44)|
|3.1.2|Media grains structure|All media essences shall be broken up into fixed-duration blocks called Media Grains.|1|[#45](https://github.com/dmf-mxl/mxl-requirements/issues/45)|

### 3.2. Video grain Format
|Section|Description|Requirements|Phase|Issue#|
|:---|:---|:---|:---|:---|
|3.2.1|Progressive video|All video essences exchanged within the platform feature progressive scan. System input and output functions that connect to the outside world may support interlaced scan formats, but all video exchanged among functions within the Media Exchange Layer shall be progressive.|1|[#46](https://github.com/dmf-mxl/mxl-requirements/issues/46)|
|3.2.2|1080p|Video Raster Format: Full HD 1920x1080, progressive scan of a single frame rate.|1|[#47](https://github.com/dmf-mxl/mxl-requirements/issues/47)|
|3.2.3|Mixed frame rates|Video Raster Format: Multiple different Frame Rates shall be supported within a single system.|2|[#48](https://github.com/dmf-mxl/mxl-requirements/issues/48)|
|3.2.4|Video Raster Format: UHD4K| 3860x2160, progressive scan|3|[#49](https://github.com/dmf-mxl/mxl-requirements/issues/49)|
|3.2.5|Custom video raster|Video Raster Format: Any H x V raster, progressive scan|3|[#50](https://github.com/dmf-mxl/mxl-requirements/issues/50)|
|3.2.6|Mixed format|The Media Exchange Layer shall support multiple video raster formats and frame rates simultaneously. Individual functions determine what formats they support and how they handle media of different video formats at their inputs|3|[#51](https://github.com/dmf-mxl/mxl-requirements/issues/51)|
|3.2.7|YUV 4:2:2 10 bit|Video Payload Formatting is transparent to YUV 4:2:2 10-bit video, is compute efficient and allow for expansion for other formats in the future|1|[#52](https://github.com/dmf-mxl/mxl-requirements/issues/52)|
|3.2.8|RGB 4:4:4 16 bits|Video Payload Formatting is Transparent to RGB 4:4:4 16 bit video|3|[#53](https://github.com/dmf-mxl/mxl-requirements/issues/53)|
|3.2.9|Include Alpha|Key (Alpha) essences can be combined with the associated video in the same Grain ( YUVK or RGBK )|3|[#54](https://github.com/dmf-mxl/mxl-requirements/issues/54)|
|3.2.10|Compressed Grain Payload|Grain payload contains compressed media (e.g. J2K, JPEG-XS or H.264 video)  to allow media functions to exchange compressed media|3|[#55](https://github.com/dmf-mxl/mxl-requirements/issues/55)|
|3.2.11|1 Frame Video Grain|Video Grain Duration = 1 Frame|1|[#56](https://github.com/dmf-mxl/mxl-requirements/issues/56)|
|3.2.12|N Video Lines strips|Video Grain Duration = Strips of N Lines <br> If needed to further improve the latency in the system|3|[#57](https://github.com/dmf-mxl/mxl-requirements/issues/57)|

### 3.3. Audio grain Format
|Section|Description|Requirements|Phase|Issue#|
|:---|:---|:---|:---|:---|
|3.3.1|Single audio channel|An audio essence carries a single audio channel. <br> Logical grouping of channels for multi-channel audio objects shall be possible from 2 to 64.|1|[#58](https://github.com/dmf-mxl/mxl-requirements/issues/58)|
|3.3.2|24 bit 48 KHz audio|Audio Payload Formatting is transparent* to 48KHz, 24-bit Linear PCM samples and compute efficient <br> (*exchange could be more than 24 bits such as 32 bit float)|1|[#59](https://github.com/dmf-mxl/mxl-requirements/issues/59)|
|3.3.3|24 bit 96 KHz audio|Audio Payload Formatting is transparent* to 96KHz, 24 bit Linear PCM samples <br> (*exchange could be more than 24 bits such as 32 bit float)|3|[#60](https://github.com/dmf-mxl/mxl-requirements/issues/60)|
|3.3.4|Configurable audio Grain duration|Audio Grain Duration configurable from 125 us to 20 ms <br> Shorter Grain durations (e.g. 125 uSec) would serve audio latency applications, and longer Grain durations (e.g. 10 mS) would reduce resource utilization for workflows that are not sensitive to audio latency.|3|[#61](https://github.com/dmf-mxl/mxl-requirements/issues/61)|

### 3.4 ANC grain format
|Section|Description|Requirements|Phase|Issue#|
|:---|:---|:---|:---|:---|
|${\color{red}3.4.1}$|TTML subtitling|Captioning/Subtitling Data formatted as TTML (Time Text Markup Language)|2|[#62](https://github.com/dmf-mxl/mxl-requirements/issues/62)|
|3.4.2|SCTE|Other Timed Metadata: SCTE Triggers formatted as JSON or other data representation.|3|[#63](https://github.com/dmf-mxl/mxl-requirements/issues/63)|

## 4. Grain Identity, Format Description and Timing
|Section|Description|Requirements|Phase|Issue#|
|:---|:---|:---|:---|:---|
|4.1|Flow has flow ID|Flow ID: Flow includes a unique ID for the media essence flow. All grains corresponding to the same essence flow have the same Flow ID.|1|[#65](https://github.com/dmf-mxl/mxl-requirements/issues/65)|
|4.2|Flow has format type or description|Format Type or Description: Each Flow is self-described by including a Format Type label or Full description of the format for the Grain. <br> This allows to distinguish between Video, Audio and Timed Metadata payloads and eventually between different video and audio formats.|1|[#66](https://github.com/dmf-mxl/mxl-requirements/issues/66)|
|${\color{red}4.3}$|Grain creation timestamp|**Format Type or Description:** Each Flow is self-described by including a Format Type label or Full description of the format for the Grain. <br> This allows to distinguish between Video, Audio and Timed Metadata payloads and eventually between different video and audio formats.|2|[#67](https://github.com/dmf-mxl/mxl-requirements/issues/67)|
|${\color{red}4.4}$|Entry timestamp|**Platform Entry Timestamp:** Each Grain includes a second timestamp representing the time the media payload was first received or generated within the platform. <br> This would allow a function to know the delay caused by these essences in the system and potentially use that delay to align multiple essences together.|2|[#68](https://github.com/dmf-mxl/mxl-requirements/issues/68)|
|4.5|User-defined timestamps|Each Grain may include additional "User-Defined" timestamps. These additional timestamps could be based on an incoming external signal's timestamps or derived by applying a user-defined offset to the Platform Entry timestamp to account for known upstream latency to capture the actual time of origin of a source.|3|[#69](https://github.com/dmf-mxl/mxl-requirements/issues/69)|
|4.6|User-defined metadata|Each Grain may include additional user-definable meta data fields to allow vendors to add additional functionality or capabilities|3|[#70](https://github.com/dmf-mxl/mxl-requirements/issues/70)|

## 5. Flow Creation and Media Write / Read Functions
|Section|Description|Requirements|Phase|Issue#|
|:---|:---|:---|:---|:---|
|5.1|CREATE Flow|Media Exchange SDK provides a function to CREATE (instantiate) a media essence flow. <br> The creation of the media flow allocates a set of buffers in shared memory. It adds the flow to the Media Exchange Layer's Flow Memory Address Table, creating a link between the Flow ID and the memory location. <br> The Create function returns the Flow ID to the Media Function. The returned Flow ID could be used to register the flow in an NMOS Registry.|1|[#72](https://github.com/dmf-mxl/mxl-requirements/issues/72)|
|5.2|READ Flow|Media Exchange SDK provides a function to READ media from an existing flow identified by a flow ID. <br> Each call to the READ function reads a single media Grain.|1|[#73](https://github.com/dmf-mxl/mxl-requirements/issues/73)|
|${\color{red}5.3}$|UPDATE Flow|Media Exchange SDK provides a function to UPDATE (write) media to a flow specifying the flow ID. <br> Each call to the UPDATE function writes a single Media Grain (Grain).|1|[#74](https://github.com/dmf-mxl/mxl-requirements/issues/74)|
|5.4|DELETE Flow|Media Exchange SDK provides a function to DELETE a media essence flow. <br> The deletion of the media flow destroys the buffers and releases the memory that had been used for the flow and removes the flow from from the Media Exchange Layer's flow table|1|[#75](https://github.com/dmf-mxl/mxl-requirements/issues/75)|

## 6. Security Considerations
|Section|Description|Requirements|Phase|Issue#|
|:---|:---|:---|:---|:---|
|${\color{red}6.1}$|authentication / authorization|All Media Exchange Layer APIs support mandatory authentication / authorization using OAuth 2.0 / JSON Web Token (JWT)|2|[#77](https://github.com/dmf-mxl/mxl-requirements/issues/77)|
|${\color{red}6.2}$|Encrypted communications|All Media Exchange Layer configuration and control data between functions is encrypted using TSL 1.3 data encryption.|2|[#78](https://github.com/dmf-mxl/mxl-requirements/issues/78)|
|${\color{red}6.3}$|Encrypted media|All Media Exchange Layer media data between functions is encrypted|3|[#79](https://github.com/dmf-mxl/mxl-requirements/issues/79)|

## Notes and misalignment from TSC (formerly tiger team)
* 1.6 TSC don't think there are plans to do this. Is the true wish from users to interconnect with full single host systems running Windows ? If so, maybe just the RDMA sender/receiver could be made cross platform without the intra-host layer.
* 2.1 Although the first milestone isn't planned to include GPU/NPU memory, the memory layout should plan for device memory to be supported from day 1. <br> We believe NPU could be out of scope for now (although shouldn't be a problem to add given the device memory support in the memory layout).
* 2.6 First target is for intra host with plan for inter-host later on. The details of the transport itself can be abstracted, but the decision to transfer a flow from one host to another belongs to another layer (out of MXL scope) that can then use the SDK to request said transfer. <br> A suitable south bound API (Libfabric??) should be added in a future iteration to allow HW vendors add support for their NICs/DPUs/FPGA cards etc.. All abstracted from application vendors.
* 2.10-11-12 Shared memory <domains> will be provided in <volumes>. This approach makes it secure, compatible with bare metal, docker and kubernetes and will allow vendors to partition deployments in separate <contexts> or <domains>
* 3.2.7 Configurable but v210 is the minimum, common, operating point.
* 3.3.1 One audio flow should "carry" all channels from a given group. It is up to the user to create single or multiple channel flows. Within a flow, all channels are stored independently, but the synchronization primitives should affect all channels simultaneously.
* 3.3.2-3 Stored as Float32.
* 3.2.4 Believe we have agreement from other vendors to aim for reader/writer selection of range of sample to address for reading and writing to a flow. Minimum range duration could be clamped to disallow abuse of the granularity.
* 3.4.1 Plan is to use SMPTE ANC Packet format by Phase 2
* 4.3-4 Grain timestamp is the "capture timestamp". It should be maintained through the entire system - even across hosts. <br> The current system time when the buffer is written to the SDK is irrelevant to media functions.
* 4.5 Would consider this as grain metadata.
* 5.3 The functionality will exist, but this cannot be a single call to allow for (R)DMA as the buffer pointer needs to be provided by the SDK, user writes data and then confirms write completion.
* 6.1-2 Belongs to upper layers to MXL
* 6.3 Out of scope for locally stored media - possible option for RDMA inter-host, at a compute cost.