# Pre-trained Neural Api Networks (Models)
This repository contains pre-trained neural network models for the [CAI neural API](https://github.com/joaopauloschuler/neural-api).

## Image classification pre-trained neural network models

| Dataset | Source Code | Input Size | Trained Model | Parameters    | Test Accuracy | 
|---------|-------------|------------|---------------|---------------|---------------|
| [Malaria](https://www.tensorflow.org/datasets/catalog/malaria)|[source](https://github.com/joaopauloschuler/neural-api/blob/master/examples/MalariaImageClassification/MalariaImageClassification.pas)|64x64x3|[Malaria-20230720](https://github.com/joaopauloschuler/pre-trained-neural-api-networks/tree/main/image-classification/malaria)|192K|95.63%|
| [Colorectal Cancer](https://www.tensorflow.org/datasets/catalog/colorectal_histology)|[source](https://github.com/joaopauloschuler/neural-api/blob/master/examples/ColorectalImageClassification/ColorectalImageClassification.pas)|64x64x3|[Colorectal-20230720](https://github.com/joaopauloschuler/pre-trained-neural-api-networks/tree/main/image-classification/colorectal-cancer)|202K|94.26%|
| [Plant Leaf Disease <br/> (Plant Village)](https://www.tensorflow.org/datasets/catalog/plant_village)|[source](https://github.com/joaopauloschuler/neural-api/blob/master/examples/SimplePlantLeafDisease/SimplePlantLeafDisease.pas)|64x64x3|[SimplePlantLeafDisease-20230720](https://github.com/joaopauloschuler/pre-trained-neural-api-networks/tree/main/image-classification/plant-leaf-disease)|252K|99.03%|

### Using Trained Models for Image Classification

The simplest way to load a trained model and classify an image is:
```
  procedure ClassifyOneImageSimple;
  var
    NN: TNNet;
    ImageFileName: string;
    NeuralFit: TNeuralImageFit;
  begin
    WriteLn('Loading Neural Network...');
    NN := TNNet.Create;
    NN.LoadFromFile('SimplePlantLeafDisease-20230720.nn');
    NeuralFit := TNeuralImageFit.Create;
    ImageFileName := 'plant/Apple___Black_rot/image (1).JPG';
    WriteLn('Processing image: ', ImageFileName);
    WriteLn(
      'The class of the image is: ',
      NeuralFit.ClassifyImageFromFile(NN, ImageFileName)
    );
    NeuralFit.Free;
    NN.Free;
  end;  
```
The above source code is located at [TestPlantLeafDiseaseTrainedModelOneImage.pas](https://github.com/joaopauloschuler/neural-api/blob/master/examples/SimplePlantLeafDisease/TestPlantLeafDiseaseTrainedModelOneImage.pas).

If you would like to test against the actual training dataset, you can follow this example:
[TestPlantLeafDiseaseTrainedModel.pas](https://github.com/joaopauloschuler/neural-api/blob/master/examples/SimplePlantLeafDisease/TestPlantLeafDiseaseTrainedModel.pas).

In the case that you need more control on how your image is classified, you can look at this more detailed example:
```
  procedure ClassifyOneImage;
  var
    NN: TNNet;
    ImageFileName: string;
    NeuralFit: TNeuralImageFit;
    vInputImage, vOutput: TNNetVolume;
    InputSizeX, InputSizeY, NumberOfClasses: integer;
  begin
    WriteLn('Loading Neural Network...');
    NN := TNNet.Create;
    NN.LoadFromFile('SimplePlantLeafDisease-20230720.nn');
    NN.DebugStructure();
    InputSizeX := NN.Layers[0].Output.SizeX;
    InputSizeY := NN.Layers[0].Output.SizeY;
    NumberOfClasses := NN.GetLastLayer().Output.Size;

    NeuralFit := TNeuralImageFit.Create;
    vInputImage := TNNetVolume.Create();
    vOutput := TNNetVolume.Create(NumberOfClasses);

    ImageFileName := 'plant/Apple___Black_rot/image (1).JPG';
    WriteLn('Loading image: ',ImageFileName);

    if LoadImageFromFileIntoVolume(
      ImageFileName, vInputImage, InputSizeX, InputSizeY,
      {EncodeNeuronalInput=}csEncodeRGB) then
    begin
      WriteLn('Classifying the image:', ImageFileName);
      vOutput.Fill(0);
      NeuralFit.ClassifyImage(NN, vInputImage, vOutput);
      WriteLn('The image belongs to the class of images: ', vOutput.GetClass());
    end
    else
    begin
      WriteLn('Failed loading image: ',ImageFileName);
    end;

    vInputImage.Free;
    vOutput.Free;
    NeuralFit.Free;

    NN.Free;
  end;
```

The trained neural network (model) is loaded with
```
    NN := TNNet.Create;
    NN.LoadFromFile('SimplePlantLeafDisease-20230720.nn');
```

The input image size is found from the loaded model with:
```
    InputSizeX := NN.Layers[0].Output.SizeX;
    InputSizeY := NN.Layers[0].Output.SizeY;
```

The number of classes is found from the loaded model with:
```
    NumberOfClasses := NN.GetLastLayer().Output.Size;
```    

The image is loaded, resized and scaled from [0,255] to [-2,+2] with:
```
    ImageFileName := 'plant/Apple___Black_rot/image (1).JPG';
    WriteLn('Loading image: ',ImageFileName);

    if LoadImageFromFileIntoVolume(
      ImageFileName, vInputImage, InputSizeX, InputSizeY,
      {EncodeNeuronalInput=}csEncodeRGB) then       
```

The NN is run with plenty of tricks specific for computer vision with:
```
      NeuralFit.ClassifyImage(NN, vInputImage, vOutput);
```

The output of the neural network is placed at `vOutput`. The actual predicted class can be found with:
```
      vOutput.GetClass()
```

