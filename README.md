# Pre-trained Neural Api Networks (Models)
This repository contains pre-trained neural network models for the [CAI neural API](https://github.com/joaopauloschuler/neural-api).

| Dataset | Source Code | Input Size | Trained Model | Test Accuracy |
|---------|-------------|------------|---------------|---------------|
| [Malaria](https://www.tensorflow.org/datasets/catalog/malaria)|[source](https://github.com/joaopauloschuler/neural-api/blob/master/examples/MalariaImageClassification/MalariaImageClassification.pas)|64x64x3|[download](https://github.com/joaopauloschuler/pre-trained-neural-api-networks/tree/main/image-classification/malaria)|95.63%|
| [Colorectal Cancer](https://www.tensorflow.org/datasets/catalog/colorectal_histology)|[source](https://github.com/joaopauloschuler/neural-api/blob/master/examples/ColorectalImageClassification/ColorectalImageClassification.pas)|64x64x3|[download](https://github.com/joaopauloschuler/pre-trained-neural-api-networks/tree/main/image-classification/colorectal-cancer)|94.26%
| [Plant Village](https://www.tensorflow.org/datasets/catalog/plant_village)|[source](https://github.com/joaopauloschuler/neural-api/blob/master/examples/SimplePlantLeafDisease/SimplePlantLeafDisease.pas)|64x64x3|[download](https://github.com/joaopauloschuler/pre-trained-neural-api-networks/tree/main/image-classification/plant-leaf-disease)|99.03%

## Using Trained Models for Image Classification

You can follow this example for classifying a single image:
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
The above source code is located at:
https://github.com/joaopauloschuler/neural-api/blob/master/examples/SimplePlantLeafDisease/TestPlantLeafDiseaseTrainedModelOneImage.pas

If you would like to test against the actual training dataset, you can follow this example:
https://github.com/joaopauloschuler/neural-api/blob/master/examples/SimplePlantLeafDisease/TestPlantLeafDiseaseTrainedModel.pas
