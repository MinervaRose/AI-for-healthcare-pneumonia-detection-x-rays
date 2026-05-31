# FDA  Submission

**Your Name: Sabrina Palis**

**Name of your Device: PneumoAssist AI**

## Algorithm Description 

### 1. General Information

**Intended Use Statement:** 
This algorithm is intended to assist radiologists in identifying the presence or absence of pneumonia on frontal-view chest X-ray images. It is not intended to provide a definitive diagnosis or to replace clinical judgment.

**Indications for Use:** 
The device is indicated for use in adult patients aged 18 years and older undergoing routine screening or diagnostic chest radiography. It is intended to be used on frontal-view chest X-rays acquired using standard radiographic techniques. The algorithm is not indicated for use in pediatric patients or patients imaged in non-frontal positions or with thoracic hardware.

**Device Limitations:** 
The model was trained exclusively on frontal-view (PA or AP) chest X-rays from the NIH dataset. These images were labeled using NLP on radiology reports, which may include inaccuracies. The model has not been validated on lateral views, pediatric cases, or non-standard imaging such as CT. Comorbidities like effusion or fibrosis may reduce performance.

**Clinical Impact of Performance:** 
False negatives may delay treatment, while false positives may result in unnecessary follow-up. The algorithm is intended solely as a decision support tool. All clinical decisions must remain with the physician.

### 2. Algorithm Design and Function


**Algorithm Flowchart:**

1. Input: DICOM file (.dcm)
2. → Verify metadata (Modality: DX, Body Part: CHEST, View Position: AP/PA)
3. → Extract pixel array
4. → Resize image to (224, 224)
5. → Normalize image using training mean (128) and std (64)
6. → Convert grayscale to RGB (3-channel)
7. → Expand dims to match model input shape (1, 224, 224, 3)
8. → Predict using CNN (VGG16 + custom head)
9. → Apply threshold (0.49) → Output class (Pneumonia or No Pneumonia)

**DICOM Checking Steps:**

- Each .dcm file is loaded using pydicom.

- Metadata fields are validated to ensure compatibility:

- Modality must be 'DX'

- View Position must be 'AP' or 'PA'

- Body Part Examined must be 'CHEST'

- Files not matching these criteria are rejected before inference.

- Pixel data is extracted only if available and readable.

**Preprocessing Steps:**

- Image resized to 224×224 pixels using skimage.transform.resize.

- Pixel values normalized using training mean 128.0 and standard deviation 64.0: (img−128.0)/64.0(img−128.0)/64.0

- Grayscale image expanded to 3 channels by duplication.

- Image reshaped to match model input: (1, 224, 224, 3).

**CNN Architecture:**

Base model: VGG16 (pretrained on ImageNet), used without top classification layers.

Custom head:

- GlobalAveragePooling2D()

- Dense(256, activation='relu')

- Dropout(0.5)

- Dense(1, activation='sigmoid') for binary classification

Initially, all VGG16 layers were frozen.

Later, top convolutional blocks were unfrozen for fine-tuning.

Final model compiled with:

- Adam optimizer (learning rate = 0.001)

- binary_crossentropy loss

- Metric: accuracy


### 3. Algorithm Training

**Parameters:**
Parameters:

    Types of augmentation used during training:

        Horizontal flip

        Small random rotations (±10°)

        Zoom (±10%)

        Width and height shifts (±5%)

    Batch size: 32

    Optimizer learning rate: 0.001 (Adam)

    Frozen layers (initial): All convolutional layers of VGG16 base

    Fine-tuned layers: Top convolutional blocks of VGG16 were later unfrozen

    Layers added to pre-existing architecture:

        GlobalAveragePooling2D()

        Dense(256, activation='relu')

        Dropout(0.5)

        Dense(1, activation='sigmoid')

<< Insert algorithm training performance visualization >> 

<< Insert P-R curve >>

**Final Threshold and Explanation:**

### 4. Databases
 (For the below, include visualizations as they are useful and relevant)

**Description of Training Dataset:** 


**Description of Validation Dataset:** 


### 5. Ground Truth



### 6. FDA Validation Plan

**Patient Population Description for FDA Validation Dataset:**

**Ground Truth Acquisition Methodology:**

**Algorithm Performance Standard:**
