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

## Plates

By ```IdeaRS.OpenModel.Connection.PlateData``` you can defined plate. This object require this values:
* `Name`- Name of plate [string]
* `Id` - unique identificator [int]
* `OriginalModelId` - unique identificator from original model [string]
* `Material` - name of already defined material in IOM [string]
* `Thickness` - thickness of plate defined in metric system(m) [double]
* `Origin` - point of local coordinate system defined in global coordinate system [IdeaRS.OpenModel.Geometry3D.Point3D]
* `AxisX` `AxisY` `AxisZ` - axixs of local cordinate system [IdeaRS.OpenModel.Geometry3D.Vector3D]
* `Region` - geomtry of plate desripted by [SVG path](https://www.w3.org/TR/SVG/paths.html) [string]

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

## Cuts

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
## Bolts

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
  TensileStressArea = 0.157,
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
