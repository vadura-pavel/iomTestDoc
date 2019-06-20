# iomTestDoc
```C#
IdeaRS.OpenModel.Connection.ConnectionPoint connection = new IdeaRS.OpenModel.Connection.ConnectionPoint();
IdeaRS.OpenModel.Geometry3D.Point3D point = new IdeaRS.OpenModel.Geometry3D.Point3D() {
  X = -2,
  Y = 3,
  Z = 3
};
point.Id = openModel.GetMaxId(point) + 1;
point.Name = point.Id.ToString();

openModel.Point3D.Add(point);
```

```C#
connection.Node = new ReferenceElement(point);
connection.Name = point.Name;
connection.Id = openModel.GetMaxId(connection) + 1;
```
## Add connection
```C#
openModel.Connections.Add(new IdeaRS.OpenModel.Connection.ConnectionData());
openModel.AddObject(connection);
```
## Beams
Inicialize Beam data
```C#
openModel.Connections[0].Beams = new List<IdeaRS.OpenModel.Connection.BeamData>();
```

Add member1D 1

```C#
IdeaRS.OpenModel.Connection.BeamData beam1Data = new IdeaRS.OpenModel.Connection.BeamData
{
  Id = 1,
  OriginalModelId = "1",
  IsAdded = false,
  MirrorY = false,
  RefLineInCenterOfGravity = false,
};
openModel.Connections[0].Beams.Add(beam1Data);

var member1 = openModel.Member1D.Find(x => x.Id == 1);
IdeaRS.OpenModel.Connection.ConnectedMember conMb = new IdeaRS.OpenModel.Connection.ConnectedMember
{
  Id = member1.Id,
  MemberId = new ReferenceElement(member1),
  IsContinuous = false,
};
connection.ConnectedMembers.Add(conMb);
```

Add member1D 3

```C#
var member3 = openModel.Member1D.Find(x => x.Id == 3);
			IdeaRS.OpenModel.Connection.ConnectedMember conMb3 = new IdeaRS.OpenModel.Connection.ConnectedMember
			{
				Id = member3.Id,
				MemberId = new ReferenceElement(member3),
				IsContinuous = true,
			};
			connection.ConnectedMembers.Add(conMb3);

			IdeaRS.OpenModel.Connection.BeamData beam2Data = new IdeaRS.OpenModel.Connection.BeamData
			{
				Id = 3,
				OriginalModelId = "3",
				IsAdded = false,
				MirrorY = false,
				RefLineInCenterOfGravity = false,
			};
			openModel.Connections[0].Beams.Add(beam2Data);
```
![Beams](images/beams.PNG?raw=true "Beam")

## Plates

By [```IdeaRS.OpenModel.Connection.PlateData```](https://idea-statica.github.io/iom/iom-api/html/fa62da56-78d0-ae76-ec1a-543e2b77497f.htm) you can define plate. 
This object requires this values:
* `Name`- name of plate [string]
* `Id` - unique identificator [int]
* `OriginalModelId` - unique identificator from original model [string]
* `Material` - name of already defined material in IOM [string]
* `Thickness` - thickness of plate defined in metric system(m) [double]
* `Origin` - point of local coordinate system defined in global coordinate system [Point3D](https://idea-statica.github.io/iom/iom-api/html/20f596b7-3cd7-56ba-be81-c712cfd9f094.htm)
* `AxisX` `AxisY` `AxisZ` - axixs of local coordinate system [Vector3D](https://idea-statica.github.io/iom/iom-api/html/5789c2c7-c7a4-30f0-f1d0-cb971aeb37bc.htm)
* `Region` - geometry of plate descript by [SVG path](https://www.w3.org/TR/SVG/paths.html) [string]

#### Example of create plate:
```C#
//add plate
IdeaRS.OpenModel.Connection.PlateData plateData = new IdeaRS.OpenModel.Connection.PlateData
{
  Name = "P1",
  Thickness = 0.02,
  Id = 11,
  Material = "S355",
  OriginalModelId = "11",
  Origin = new IdeaRS.OpenModel.Geometry3D.Point3D
  {
    X = -1.87,
    Y = 2.88,
    Z = 2.7
  },
  AxisX = new IdeaRS.OpenModel.Geometry3D.Vector3D
  {
    X = 0,
    Y = 1,
    Z = 0
  },
  AxisY = new IdeaRS.OpenModel.Geometry3D.Vector3D
  {
    X = 0,
    Y = 0,
    Z = 1
  },
  AxisZ = new IdeaRS.OpenModel.Geometry3D.Vector3D
  {
    X = 1,
    Y = 0,
    Z = 0
  },
  Region = "M 0 0 L 0.24 0 L 0.24 0.5 L 0 0.5 L 0 0",
};

(openModel.Connections[0].Plates ?? 
  (openModel.Connections[0].Plates = new List<IdeaRS.OpenModel.Connection.PlateData>())).Add(plateData);
```
![Plate](images/plate.PNG?raw=true "Plate")

## Cuts
### Cut beam by beam or plate 
By ```IdeaRS.OpenModel.Connection.CutBeamByBeamData``` you can define cuting beam by beam or plate. 
This object requires this values:
* `CuttingObject`- reference to beam or plate whitch is use for cutting [[ReferenceElement](https://idea-statica.github.io/iom/iom-api/html/9e905b1a-a8a6-ae75-4c62-221258514c0b.htm)]
* `ModifiedObject`- reference to beam whitch is modified by cutting [[ReferenceElement](https://idea-statica.github.io/iom/iom-api/html/9e905b1a-a8a6-ae75-4c62-221258514c0b.htm)]
* `IsWeld`- flags for welding cut [bool]

#### Example of create cut beam by plate:
```#C
// add cut
openModel.Connections[0].CutBeamByBeams = new List<IdeaRS.OpenModel.Connection.CutBeamByBeamData>
{
  new IdeaRS.OpenModel.Connection.CutBeamByBeamData
  {
    CuttingObject = new ReferenceElement(plateData),
    ModifiedObject = new ReferenceElement(beam1Data),
    IsWeld = true,
  }
};
```
![Cut Beam By Beam](images/cutBeamByPlate.PNG?raw=true "Cut Beam By Beam")

### Cut beam by workplane
By [```IdeaRS.OpenModel.Connection.CutData```](https://idea-statica.github.io/iom/iom-api/html/ccc12bf8-7d60-c423-f975-b6cc90cf2b3c.htm) you can define cuting beam by workplane.
This object requires this values:
* `PlanePoint`- point defines in global coordinate system place where is workplane  [Point3D](https://idea-statica.github.io/iom/iom-api/html/20f596b7-3cd7-56ba-be81-c712cfd9f094.htm)
* `NormalVector`- normal vector of workplane [Vector3D](https://idea-statica.github.io/iom/iom-api/html/5789c2c7-c7a4-30f0-f1d0-cb971aeb37bc.htm)

#### Example of create cut beam by worplane:

```#C
CutData cutData = new CutData(){
	new IdeaRS.OpenModel.Geometry3D.Point3D() { X = 0, Y = 0, Z = 0 },
	new IdeaRS.OpenModel.Geometry3D.Vector3D() { X = 1, Y = 0, Z = 0 },
}
(beam1Data.Cuts ?? (beam1Data.Cuts = new List<CutData>())).Add(cutData);
```

## Bolts
By [```IdeaRS.OpenModel.Connection.BoltGrid```](https://idea-statica.github.io/iom/iom-api/html/b9b49fac-1350-22fd-8ed4-9938ce6345d1.htm) you can define bolts and connect objects. 
This object requires this values:
* `Id` - unique identificator [int]
* `Diameter` - diameter of bolt(m) [double]
* `DiagonalHeadDiameter` - head diameter of bolt(m) [double]
* `HeadHeight` - height head of bolt(m) [double]
* `BoreHole` - hole for bolt(m) [double]
* `TensileStressArea` - tensile stress area(mm2) [double]
* `NutThickness` - nut thickness(m) [double]
* `AnchorLen` - lenght of bolt/anchor(m) [double]
* `Material` - name of already defined material in IOM [string]
* `Standard` - standard of bolt [string]
* `Origin` - point of local coordinate system defined in global coordinate system [[Point3D](https://idea-statica.github.io/iom/iom-api/html/20f596b7-3cd7-56ba-be81-c712cfd9f094.htm)]
* `AxisX` `AxisY` `AxisZ` - axis of local coordinate  system [[Vector3D](https://idea-statica.github.io/iom/iom-api/html/5789c2c7-c7a4-30f0-f1d0-cb971aeb37bc.htm)]
* `Positions` - list of bolt positions define by points in global coordinate system [[List Point3D](https://idea-statica.github.io/iom/iom-api/html/20f596b7-3cd7-56ba-be81-c712cfd9f094.htm)]
* `ConnectedPartIds` - list of identificators object which want to connect together [string]


#### Example of create boltgrid:
```C#
IdeaRS.OpenModel.Connection.BoltGrid boltGrid = new IdeaRS.OpenModel.Connection.BoltGrid()
{
  Id = 41,
  ConnectedPartIds = new List<string>(),
  Diameter = 0.016,
  HeadDiameter = 0.024,
  DiagonalHeadDiameter = 0.026,
  HeadHeight = 0.01,
  BoreHole = 0.018,
  TensileStressArea = 157,
  NutThickness = 0.013,
  AnchorLen = 0.05,
  Material = "8.8",
  Standard = "M 16",
};

boltGrid.Origin = new IdeaRS.OpenModel.Geometry3D.Point3D() { X = plateData.Origin.X, Y = plateData.Origin.Y, Z = plateData.Origin.Z };
boltGrid.AxisX = new IdeaRS.OpenModel.Geometry3D.Vector3D() { X = plateData.AxisX.X, Y = plateData.AxisX.Y, Z = plateData.AxisX.Z };
boltGrid.AxisY = new IdeaRS.OpenModel.Geometry3D.Vector3D() { X = plateData.AxisY.X, Y = plateData.AxisY.Y, Z = plateData.AxisY.Z };
boltGrid.AxisZ = new IdeaRS.OpenModel.Geometry3D.Vector3D() { X = plateData.AxisZ.X, Y = plateData.AxisZ.Y, Z = plateData.AxisZ.Z };
boltGrid.Positions = new List<IdeaRS.OpenModel.Geometry3D.Point3D>
{
  new IdeaRS.OpenModel.Geometry3D.Point3D()
  {
    X = -1.87,
    Y = 2.92,
    Z = 2.8
  },
  new IdeaRS.OpenModel.Geometry3D.Point3D()
  {
    X = -1.87,
    Y = 3.08,
    Z = 2.8
  },
  new IdeaRS.OpenModel.Geometry3D.Point3D()
  {
    X = -1.87,
    Y = 2.92,
    Z = 3.15
  },
  new IdeaRS.OpenModel.Geometry3D.Point3D()
  {
    X = -1.87,
    Y = 3.08,
    Z = 3.15
  }
};

boltGrid.ConnectedPartIds = new List<string>() { beam2Data.OriginalModelId, plateData.OriginalModelId };

(openModel.Connections[0].BoltGrids ?? (openModel.Connections[0].BoltGrids = new List<IdeaRS.OpenModel.Connection.BoltGrid>())).Add(boltGrid);
```
![Bolts](images/bolts.PNG?raw=true "Bolts")

## Welds
### Weld 
By [```IdeaRS.OpenModel.Connection.WeldData```](https://idea-statica.github.io/iom/iom-api/html/c8c8c54b-d021-d04f-85a3-8e410e7a0170.htm) you can define weld. 
This object requires this values:
* `Id` - unique identificator [int]
* `ConnectedPartIds` - list of identificators object which want to welded together [string]
* `Start` - point of start weld defined in global coordinate system [[Point3D](https://idea-statica.github.io/iom/iom-api/html/20f596b7-3cd7-56ba-be81-c712cfd9f094.htm)]
* `End` - point of end weld defined in global coordinate system [[Point3D](https://idea-statica.github.io/iom/iom-api/html/20f596b7-3cd7-56ba-be81-c712cfd9f094.htm)]
* `Thickness` - thickness of weld defined in metric system(m) [double]
* `WeldType` - type of weld [[WeldType](https://idea-statica.github.io/iom/iom-api/html/722ccc5e-a301-19b2-2da0-00969bf409b3.htm)]

#### Example of create stiffeners with welds:

```#C
//add plate 2
IdeaRS.OpenModel.Connection.PlateData plateData2 = new IdeaRS.OpenModel.Connection.PlateData
{
Name = "P2",
Thickness = 0.02,
Id = 12,
Material = "S355",
OriginalModelId = "12",
Origin = new IdeaRS.OpenModel.Geometry3D.Point3D
{
	X = -2.103,
	Y = 2.88,
	Z = 2.75
},
AxisX = new IdeaRS.OpenModel.Geometry3D.Vector3D
{
	X = 1,
	Y = 0,
	Z = 0
},
AxisY = new IdeaRS.OpenModel.Geometry3D.Vector3D
{
	X = 0,
	Y = 1,
	Z = 0
},
AxisZ = new IdeaRS.OpenModel.Geometry3D.Vector3D
{
	X = 0,
	Y = 0,
	Z = 1
},
Region = "M 0 0 L 0.206 0 L 0.206 0.105 L 0.195 0.115 L 0.011 0.115 L 0.0 0.105 L 0 0",
};

(openModel.Connections[0].Plates ?? (openModel.Connections[0].Plates = new List<IdeaRS.OpenModel.Connection.PlateData>())).Add(plateData2);
//add weld between memeber 2 and plate 2 - stiffener
IdeaRS.OpenModel.Connection.WeldData weldData = new IdeaRS.OpenModel.Connection.WeldData()
{
Id = 31,
ConnectedPartIds = new List<string>() {  plateData2.OriginalModelId, beam2Data.OriginalModelId },
Start = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2,
	Y = 2.995,
	Z = 2.76
},
End = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2,
	Y = 2.995,
	Z = 2.76
},
Thickness = 0.004,
WeldType = IdeaRS.OpenModel.Connection.WeldType.DoubleFillet,
};
(openModel.Connections[0].Welds ?? (openModel.Connections[0].Welds = new List<IdeaRS.OpenModel.Connection.WeldData>())).Add(weldData);

//add weld3 between memeber 2 and plate 2 - stiffener
IdeaRS.OpenModel.Connection.WeldData weldData3 = new IdeaRS.OpenModel.Connection.WeldData()
{
Id = 33,
ConnectedPartIds = new List<string>() { plateData2.OriginalModelId, beam2Data.OriginalModelId },
Start = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2.103,
	Y = 2.90,
	Z = 2.76
},
End = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2.103,
	Y = 2.90,
	Z = 2.76
},
Thickness = 0.004,
WeldType = IdeaRS.OpenModel.Connection.WeldType.DoubleFillet,
};
openModel.Connections[0].Welds.Add(weldData3);

//add weld4 between memeber 2 and plate 2 - stiffener
IdeaRS.OpenModel.Connection.WeldData weldData4 = new IdeaRS.OpenModel.Connection.WeldData()
{
Id = 34,
ConnectedPartIds = new List<string>() { plateData2.OriginalModelId, beam2Data.OriginalModelId },
Start = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -1.897,
	Y = 2.90,
	Z = 2.76
},
End = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -1.897,
	Y = 2.90,
	Z = 2.76
},
Thickness = 0.004,
WeldType = IdeaRS.OpenModel.Connection.WeldType.DoubleFillet,
};
openModel.Connections[0].Welds.Add(weldData4);

//add plate 3
IdeaRS.OpenModel.Connection.PlateData plateData3 = new IdeaRS.OpenModel.Connection.PlateData
{
Name = "P3",
Thickness = 0.02,
Id = 13,
Material = "S355",
OriginalModelId = "13",
Origin = new IdeaRS.OpenModel.Geometry3D.Point3D
{
	X = -2.103,
	Y = 2.88,
	Z = 3.1
},
AxisX = new IdeaRS.OpenModel.Geometry3D.Vector3D
{
	X = 1,
	Y = 0,
	Z = 0
},
AxisY = new IdeaRS.OpenModel.Geometry3D.Vector3D
{
	X = 0,
	Y = 1,
	Z = 0
},
AxisZ = new IdeaRS.OpenModel.Geometry3D.Vector3D
{
	X = 0,
	Y = 0,
	Z = 1
},
Region = "M 0 0 L 0.206 0 L 0.206 0.105 L 0.195 0.115 L 0.011 0.115 L 0.0 0.105 L 0 0",
};
openModel.Connections[0].Plates.Add(plateData3);

//add weld between memeber 2 and plate 3 - stiffener
IdeaRS.OpenModel.Connection.WeldData weldData2 = new IdeaRS.OpenModel.Connection.WeldData()
{
Id = 32,
ConnectedPartIds = new List<string>() { plateData3.OriginalModelId, beam2Data.OriginalModelId },
Start = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2,
	Y = 2.995,
	Z = 3.11
},
End = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2,
	Y = 2.995,
	Z = 3.11
},
Thickness = 0.004,
WeldType = IdeaRS.OpenModel.Connection.WeldType.DoubleFillet,
};
openModel.Connections[0].Welds.Add(weldData2);

//add weld5 between memeber 2 and plate 3 - stiffener
IdeaRS.OpenModel.Connection.WeldData weldData5 = new IdeaRS.OpenModel.Connection.WeldData()
{
Id = 35,
ConnectedPartIds = new List<string>() { plateData3.OriginalModelId, beam2Data.OriginalModelId },
Start = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2.103,
	Y = 2.90,
	Z = 3.11
},
End = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2.103,
	Y = 2.90,
	Z = 3.11
},
Thickness = 0.004,
WeldType = IdeaRS.OpenModel.Connection.WeldType.DoubleFillet,
};
openModel.Connections[0].Welds.Add(weldData5);

//add weld6 between memeber 2 and plate 3 - stiffener
IdeaRS.OpenModel.Connection.WeldData weldData6 = new IdeaRS.OpenModel.Connection.WeldData()
{
Id = 36,
ConnectedPartIds = new List<string>() { plateData3.OriginalModelId, beam2Data.OriginalModelId },
Start = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -1.897,
	Y = 2.90,
	Z = 3.11
},
End = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -1.897,
	Y = 2.90,
	Z = 3.11
},
Thickness = 0.004,
WeldType = IdeaRS.OpenModel.Connection.WeldType.DoubleFillet,
};
openModel.Connections[0].Welds.Add(weldData6);

//add plate 4
IdeaRS.OpenModel.Connection.PlateData plateData4 = new IdeaRS.OpenModel.Connection.PlateData
{
Name = "P4",
Thickness = 0.02,
Id = 14,
Material = "S355",
OriginalModelId = "14",
Origin = new IdeaRS.OpenModel.Geometry3D.Point3D
{
	X = -2.103,
	Y = 3.12,
	Z = 2.75
},
AxisX = new IdeaRS.OpenModel.Geometry3D.Vector3D
{
	X = 1,
	Y = 0,
	Z = 0
},
AxisY = new IdeaRS.OpenModel.Geometry3D.Vector3D
{
	X = 0,
	Y = -1,
	Z = 0
},
AxisZ = new IdeaRS.OpenModel.Geometry3D.Vector3D
{
	X = 0,
	Y = 0,
	Z = 1
},
Region = "M 0 0 L 0.206 0 L 0.206 0.105 L 0.195 0.115 L 0.011 0.115 L 0.0 0.105 L 0 0",
};
openModel.Connections[0].Plates.Add(plateData4);

//add weld7 between memeber 2 and plate 4 - stiffener
IdeaRS.OpenModel.Connection.WeldData weldData7 = new IdeaRS.OpenModel.Connection.WeldData()
{
Id = 37,
ConnectedPartIds = new List<string>() { plateData4.OriginalModelId, beam2Data.OriginalModelId },
Start = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2,
	Y = 3.005,
	Z = 2.76
},
End = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2,
	Y = 3.005,
	Z = 2.76
},
Thickness = 0.004,
WeldType = IdeaRS.OpenModel.Connection.WeldType.DoubleFillet,
};
openModel.Connections[0].Welds.Add(weldData7);

//add weld8 between memeber 2 and plate 4 - stiffener
IdeaRS.OpenModel.Connection.WeldData weldData8 = new IdeaRS.OpenModel.Connection.WeldData()
{
Id = 38,
ConnectedPartIds = new List<string>() { plateData4.OriginalModelId, beam2Data.OriginalModelId },
Start = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2.103,
	Y = 3.1,
	Z = 2.76
},
End = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2.103,
	Y = 3.1,
	Z = 2.76
},
Thickness = 0.004,
WeldType = IdeaRS.OpenModel.Connection.WeldType.DoubleFillet,
};
openModel.Connections[0].Welds.Add(weldData8);

//add weld9 between memeber 2 and plate 4 - stiffener
IdeaRS.OpenModel.Connection.WeldData weldData9 = new IdeaRS.OpenModel.Connection.WeldData()
{
Id = 39,
ConnectedPartIds = new List<string>() { plateData4.OriginalModelId, beam2Data.OriginalModelId },
Start = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -1.897,
	Y = 3.1,
	Z = 2.76
},
End = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -1.897,
	Y = 3.1,
	Z = 2.76
},
Thickness = 0.004,
WeldType = IdeaRS.OpenModel.Connection.WeldType.DoubleFillet,
};
openModel.Connections[0].Welds.Add(weldData9);

//add plate 5
IdeaRS.OpenModel.Connection.PlateData plateData5 = new IdeaRS.OpenModel.Connection.PlateData
{
Name = "P5",
Thickness = 0.02,
Id = 15,
Material = "S355",
OriginalModelId = "15",
Origin = new IdeaRS.OpenModel.Geometry3D.Point3D
{
	X = -2.103,
	Y = 3.12,
	Z = 3.1
},
AxisX = new IdeaRS.OpenModel.Geometry3D.Vector3D
{
	X = 1,
	Y = 0,
	Z = 0
},
AxisY = new IdeaRS.OpenModel.Geometry3D.Vector3D
{
	X = 0,
	Y = -1,
	Z = 0
},
AxisZ = new IdeaRS.OpenModel.Geometry3D.Vector3D
{
	X = 0,
	Y = 0,
	Z = 1
},
Region = "M 0 0 L 0.206 0 L 0.206 0.105 L 0.195 0.115 L 0.011 0.115 L 0.0 0.105 L 0 0",
};
openModel.Connections[0].Plates.Add(plateData5);

//add weld10 between memeber 2 and plate 5 - stiffener
IdeaRS.OpenModel.Connection.WeldData weldData10 = new IdeaRS.OpenModel.Connection.WeldData()
{
Id = 40,
ConnectedPartIds = new List<string>() { plateData5.OriginalModelId, beam2Data.OriginalModelId },
Start = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2,
	Y = 3.005,
	Z = 3.11
},
End = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2,
	Y = 3.005,
	Z = 3.11
},
Thickness = 0.004,
WeldType = IdeaRS.OpenModel.Connection.WeldType.DoubleFillet,
};
openModel.Connections[0].Welds.Add(weldData10);

//add weld11 between memeber 2 and plate 5 - stiffener
IdeaRS.OpenModel.Connection.WeldData weldData11 = new IdeaRS.OpenModel.Connection.WeldData()
{
Id = 41,
ConnectedPartIds = new List<string>() { plateData5.OriginalModelId, beam2Data.OriginalModelId },
Start = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2.103,
	Y = 3.10,
	Z = 3.11
},
End = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -2.103,
	Y = 3.10,
	Z = 3.11
},
Thickness = 0.004,
WeldType = IdeaRS.OpenModel.Connection.WeldType.DoubleFillet,
};
openModel.Connections[0].Welds.Add(weldData11);

//add weld12 between memeber 2 and plate 5 - stiffener
IdeaRS.OpenModel.Connection.WeldData weldData12 = new IdeaRS.OpenModel.Connection.WeldData()
{
Id = 46,
ConnectedPartIds = new List<string>() { plateData5.OriginalModelId, beam2Data.OriginalModelId },
Start = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -1.897,
	Y = 3.10,
	Z = 3.11
},
End = new IdeaRS.OpenModel.Geometry3D.Point3D()
{
	X = -1.897,
	Y = 3.10,
	Z = 3.11
},
Thickness = 0.004,
WeldType = IdeaRS.OpenModel.Connection.WeldType.DoubleFillet,
};
openModel.Connections[0].Welds.Add(weldData12);
```

![Stiffeners With Welds](images/stiffenersWithWelds.PNG?raw=true "Stiffeners With Welds")
