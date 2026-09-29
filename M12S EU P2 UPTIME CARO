using Dalamud.Bindings.ImGui;
using Dalamud.Game.ClientState.Objects.Types;
using ECommons;
using ECommons.Configuration;
using ECommons.DalamudServices;
using ECommons.GameFunctions;
using ECommons.Hooks.ActionEffectTypes;
using ECommons.ImGuiMethods;
using ECommons.Logging;
using ECommons.MathHelpers;
using ECommons.StringHelpers;
using ECommons.Throttlers;
using FFXIVClientStructs.FFXIV.Client.Game;
using Splatoon.Data;
using Splatoon.Memory;
using Splatoon.SplatoonScripting;
using Splatoon.Utility;
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.Linq;
using System.Numerics;
using System.Text;
using System.Text.RegularExpressions;
using Dalamud.Game.ClientState.Conditions;
using Dalamud.Game.ClientState.Objects.SubKinds;
using Splatoon;
using static Splatoon.Splatoon;
using TerraFX.Interop.Windows;
using Dalamud.Interface.Colors;
using ECommons.GameHelpers;
using Newtonsoft.Json;

using ECommons.DalamudServices.Legacy;

namespace SplatoonScriptsOfficial.Duties.Dawntrail;

public unsafe class M12S_P2_田园牧歌_疲惫 : SplatoonScript
{
    public override Metadata Metadata { get; } = new(22, "NightmareXIV, Redmoon, Garume");
    public override HashSet<uint>? ValidTerritories { get; } = [1327];

    public override void OnSetup()
    {
        Controller.RegisterElementFromCode("DefamationGroup1", """
            {"Name":"变更我的位置！","refX":86.5,"refY":113.5,"radius":1.0,"Donut":0.5,"fillIntensity":0.548,"thicc":4,"tether":true,"overlayText":"诽谤 1"}
            """);
        Controller.RegisterElementFromCode("DefamationGroup2", """
            {"Name":"变更我的位置！","refX":113.5,"refY":113.5,"radius":1.0,"Donut":0.5,"fillIntensity":0.548,"thicc":4,"tether":true,"overlayText":"诽谤 2"}
            """);
        Controller.RegisterElementFromCode("StackGroup1", """
            {"Name":"变更我的位置！","refX":92.0,"refY":100.0,"radius":1.0,"Donut":0.5,"fillIntensity":0.548,"thicc":4,"tether":true,"overlayText":"分摊 1"}
            """);
        Controller.RegisterElementFromCode("StackGroup2", """
            {"Name":"变更我的位置！","refX":108.0,"refY":100.0,"radius":1.0,"Donut":0.5,"fillIntensity":0.548,"thicc":4,"tether":true,"overlayText":"分摊 2"}
            """);
        Controller.RegisterElementFromCode("SafespotGroup1", """
            {"Name":"变更我的位置！","refX":100.0,"refY":91.0,"radius":1.0,"Donut":0.5,"fillIntensity":0.548,"thicc":4,"tether":true,"overlayText":"安全点 1"}
            """);
        Controller.RegisterElementFromCode("SafespotGroup2", """
            {"Name":"变更我的位置！","refX":100.0,"refY":91.0,"radius":1.0,"Donut":0.5,"fillIntensity":0.548,"thicc":4,"tether":true,"overlayText":"安全点 2"}
            """);
        
        Controller.RegisterElementFromCode("Given Far",
            """{"Name":"(风塔) 变更我的位置！","refX":108.669846,"refY":92.17644,"Donut":0.2}""");
        Controller.RegisterElementFromCode("Given Near",
            """{"Name":"(死炮塔) 变更我的位置！","refX":108.56247,"refY":97.35193,"Donut":0.2}""");
        Controller.RegisterElementFromCode("Taken Far",
            """{"Name":"(土火塔) 变更我的位置！","refX":108.244225,"refY":107.46305,"refZ":3.8146973E-06,"Donut":0.2}""");
        Controller.RegisterElementFromCode("Taken Near",
            """{"Name":"(土火塔) 变更我的位置！","refX":110.42621,"refY":97.36201,"refZ":3.8146973E-06,"Donut":0.2}""");

        Controller.RegisterElementFromCode("DefamationOnYou", """
            {"Name":"","type":1,"radius":0.0,"Donut":0,"fillIntensity":0.548,"overlayBGColor":4286382206,"overlayTextColor":4294967295,"overlayVOffset":2.0,"overlayText":"<<< 诽谤 >>>\\n  <<< 在 你 身上! >>>","refActorType":1}
            """);
        for(int i = 0; i < 2; i++)
        {
            Controller.RegisterElementFromCode($"Defamation{i+1}", """
                {"Name":"","refX":112.58201,"refY":113.83432,"refZ":-6.1035156E-05,"radius":19.0,"Donut":1.0,"color":3372155112,"fillIntensity":0.592}
                """);
            Controller.RegisterElementFromCode($"Stack{i + 1}", """
                {"Name":"","refX":106.86787,"refY":99.954346,"radius":4.5,"Donut":0.5,"color":3358850816,"fillIntensity":0.319}
                """);
        }
        Controller.RegisterElementFromCode("PickTether", """
                {"Name":"","type":2,"refX":100.0,"refY":100.0,"offX":100.0,"offY":100.0,"radius":0.0,"color":3356425984,"fillIntensity":0.5,"thicc":8.0}
                """);
        Controller.RegisterElementFromCode("PickTetherCircle", """
                {"Name":"","type":1,"offY":2.0,"radius":2.5,"Donut":0.5,"color":3356425984,"fillIntensity":0.5,"overlayBGColor":4278190080,"overlayTextColor":4278255376,"thicc":6.0,"overlayText":"拾取此连线","refActorComparisonType":2,"includeRotation":true}
                """);

        Controller.RegisterElementFromCode("PortalConeNS1", """
            {"Name":"","type":5,"refX":100.0,"refY":92.5,"radius":40.0,"coneAngleMin":-45,"coneAngleMax":45,"includeRotation":true}
            """);

        Controller.RegisterElementFromCode("PortalConeNS2", """
            {"Name":"","type":5,"refX":100.0,"refY":92.5,"radius":40.0,"coneAngleMin":-45,"coneAngleMax":45,"includeRotation":true,"AdditionalRotation":3.1415927}
            """);

        Controller.RegisterElementFromCode("PortalConeEW1", """
            {"Name":"","type":5,"refX":100.0,"refY":92.5,"radius":40.0,"coneAngleMin":-45,"coneAngleMax":45,"includeRotation":true,"AdditionalRotation":4.712389}
            """);

        Controller.RegisterElementFromCode("PortalConeEW2", """
            {"Name":"","type":5,"refX":100.0,"refY":92.5,"radius":40.0,"coneAngleMin":-45,"coneAngleMax":45,"includeRotation":true,"AdditionalRotation":1.5707964}
            """);

        Controller.RegisterElementFromCode("Cone1", """{"Name":"","type":5,"refX":100.0,"refY":92.5,"radius":40.0,"coneAngleMin":-45,"coneAngleMax":45,"includeRotation":true,"AdditionalRotation":1.5707964}""");

        Controller.RegisterElementFromCode("Cone2", """{"Name":"","type":5,"refX":100.0,"refY":92.5,"radius":40.0,"coneAngleMin":-45,"coneAngleMax":45,"includeRotation":true,"AdditionalRotation":1.5707964}""");
        Controller.RegisterElementFromCode("Cone3", """{"Name":"","type":5,"refX":100.0,"refY":92.5,"radius":40.0,"coneAngleMin":-45,"coneAngleMax":45,"includeRotation":true,"AdditionalRotation":1.5707964}""");

        Controller.RegisterElementFromCode("Cone4", """{"Name":"","type":5,"refX":100.0,"refY":92.5,"radius":40.0,"coneAngleMin":-45,"coneAngleMax":45,"includeRotation":true,"AdditionalRotation":1.5707964}""");
        Controller.RegisterElementFromCode("Circle", """{"Name":"","Enabled":false,"refX":86.0,"refY":100.0,"radius":10.0,"tether":false}""");

        Controller.RegisterElementFromCode("TowerTether",
            "{\"Name\":\"\",\"refX\":81.732285,\"refY\":95.71492,\"radius\":3.0,\"Donut\":0.4,\"color\":3355508509,\"fillIntensity\":0.5,\"thicc\":5.0,\"tether\":true}");
        Controller.RegisterElementFromCode("Cone1", """{"Name":"","type":5,"refX":100.0,"refY":92.5,"radius":40.0,"coneAngleMin":-45,"coneAngleMax":45,"includeRotation":true,"AdditionalRotation":1.5707964}""");

        Controller.RegisterElementFromCode("P7AOERadius",
            "{\"Name\":\"\",\"type\":1,\"radius\":6.3,\"Donut\":0.2,\"fillIntensity\":0.5,\"thicc\":5.0,\"refActorType\":1,\"DistanceMax\":25.199999}");
        
        Controller.RegisterElementFromCode("Rock1",
            """{"Name":"","refX":118.829,"refY":95.482,"refZ":3.8146973E-06,"radius":4.0,"fillIntensity":0.7,"thicc":5.0}""");
        Controller.RegisterElementFromCode("Rock2",
            """{"Name":"","refX":118.829,"refY":95.482,"refZ":3.8146973E-06,"radius":4.0,"fillIntensity":0.7,"thicc":5.0}""");

        Controller.RegisterElementFromCode("FarCone1",
            "{\"Name\":\"\",\"type\":4,\"radius\":60.0,\"coneAngleMin\":-15,\"coneAngleMax\":15,\"color\":3372155131,\"fillIntensity\":0.15,\"includeRotation\":true,\"FaceMe\":true}");
        Controller.RegisterElementFromCode("FarCone2",
            "{\"Name\":\"\",\"type\":4,\"radius\":60.0,\"coneAngleMin\":-15,\"coneAngleMax\":15,\"color\":3372155131,\"fillIntensity\":0.15,\"includeRotation\":true,\"FaceMe\":true}");
        Controller.RegisterElementFromCode("NearCone1",
            "{\"Name\":\"\",\"type\":4,\"radius\":60.0,\"coneAngleMin\":-15,\"coneAngleMax\":15,\"color\":3372155131,\"fillIntensity\":0.15,\"includeRotation\":true,\"FaceMe\":true}");
        Controller.RegisterElementFromCode("NearCone2",
            "{\"Name\":\"\",\"type\":4,\"radius\":60.0,\"coneAngleMin\":-15,\"coneAngleMax\":15,\"color\":3372155131,\"fillIntensity\":0.15,\"includeRotation\":true,\"FaceMe\":true}");

        Controller.RegisterElement("stack tether", new Element(0)
        {
            thicc = 5f,
            radius = 4.5f,
            tether = true,
            Filled = false,
            Donut = 0.5f,
        });
        Controller.RegisterElement("p7sub1 tether", new Element(0)
        {
            thicc = 5f,
            radius = 0.35f,
            tether = true,
            Filled = true,
            Donut = 0.2f,
        });

        Controller.RegisterElementFromCode("SafeWestLeftRight", """{"Name":"","refX":85.0,"refY":95.0,"radius":3.0,"color":3366322069,"Filled":false,"fillIntensity":0.5,"overlayText":"安全区: 西侧平台 - 左/右"}""");
        Controller.RegisterElementFromCode("SafeEastLeftRight", """{"Name":"","refX":115.0,"refY":95.0,"radius":3.0,"color":3366322069,"Filled":false,"fillIntensity":0.5,"overlayText":"安全区: 东侧平台 - 左/右"}""");
        Controller.RegisterElementFromCode("SafeWestFrontBack", """{"Name":"","refX":92.0,"refY":100.0,"radius":3.0,"color":3366322069,"Filled":false,"fillIntensity":0.5,"overlayText":"安全区: 西侧平台 - 前后"}""");
        Controller.RegisterElementFromCode("SafeEastFrontBack", """{"Name":"","refX":108.0,"refY":100.0,"radius":3.0,"color":3366322069,"Filled":false,"fillIntensity":0.5,"overlayText":"安全区: 东侧平台 - 前后"}""");

        Controller.RegisterElementFromCode("SafeWestLeftRightA", """{"Name":"","refX":85.0,"refY":95.0,"radius":3.0,"Donut":1.0,"color":3358064384,"fillIntensity":0.663,"thicc":5.0,"tether":true}""");
        Controller.RegisterElementFromCode("SafeEastLeftRightA", """{"Name":"","refX":115.0,"refY":95.0,"radius":3.0,"Donut":1.0,"color":3358064384,"fillIntensity":0.663,"thicc":5.0,"tether":true}""");
        Controller.RegisterElementFromCode("SafeWestFrontBackA", """{"Name":"","refX":92.0,"refY":100.0,"radius":3.0,"Donut":1.0,"color":3358064384,"fillIntensity":0.663,"thicc":5.0,"tether":true}""");
        Controller.RegisterElementFromCode("SafeEastFrontBackA", """{"Name":"","refX":108.0,"refY":100.0,"radius":3.0,"Donut":1.0,"color":3358064384,"fillIntensity":0.663,"thicc":5.0,"tether":true}""");
    }

    Dictionary<Direction, Vector2> ReenactmentDirections = new()
    {
        [Direction.N] = new(100, 86),
        [Direction.NE] = new(110, 90),
        [Direction.E] = new(114, 100),
        [Direction.SE] = new(110, 110),
        [Direction.S] = new(100, 114),
        [Direction.SW] = new(90, 110),
        [Direction.W] = new(86, 100),
        [Direction.NW] = new(90, 90),
    };

    public enum DataIds
    {
        PlayerClone = 19210,
    }

    public enum Towers
    {
        WindLight = 2015013,
        DoomLight = 2015014,
        Fire = 2015016,
        Earth = 2015015,
    }
    
    public class TowerData(Direction side, Towers kinds)
    {
        public Direction Side = side;
        public Towers kinds = kinds;
        public Vector3 Position = Vector3.Zero;
        public uint AssignToPlayerEntityId = 0;
    }

    public enum Direction { N, NE, E, SE, S, SW, W, NW }
    public enum TetherKind
    {
        Stack = 369,
        Defamation = 368,
    }

    public class StateDef
    {
        public int PlayerPosition = -1;
        public Vector3? NextAOE = null;
        public bool? NextCleavesNorthSouth = null;
        public bool? IsCardinalFirst = null;
        public bool? IsThDecreasingResistance = null;
        public bool? IsConeSafeNorth = null;
        public HashSet<(Vector3 Pos, float Rot)> NextCleavesList = [];
        public Dictionary<uint, Vector3> ClonePositions = [];
        public Dictionary<uint, bool> DefamationPlayers = [];
        public Dictionary<uint, int> PlayerOrder = [];
        public int DefamationAttack = 0;
        public int Phase7Sub = 0; // 0 - Avoid Cone, 1 - Tower Tether
        public int Phase11Sub = 0; // 0 - Taken Tower, 1 - Wait Tower Effects, 2 - Taken Cone, 3 - End
        public int Phase = 0;
        public (string, uint)[]? TowersDebug;
        public Towers? DebugOverWriteTower = null;
        public int NumVisibleClones = 0;

        public TowerData[] TowerDataArray =
        [
            new(Direction.W, Towers.Fire),
            new(Direction.W, Towers.Earth),
            new(Direction.W, Towers.WindLight),
            new(Direction.W, Towers.DoomLight),
            new(Direction.E, Towers.Fire),
            new(Direction.E, Towers.Earth),
            new(Direction.E, Towers.WindLight),
            new(Direction.E, Towers.DoomLight),
        ];
    }


    public StateDef State = new();

    public override void OnReset()
    {
        State = new();
    }

    IEnumerable<IBattleNpc> AllClones => Svc.Objects.OfType<IBattleNpc>().Where(x => x.IsCharacterVisible() && x.DataId == (uint) DataIds.PlayerClone);



    public override void OnActorControl(uint sourceId, uint command, uint p1, uint p2, uint p3, uint p4, uint p5, uint p6, uint p7, uint p8, ulong targetId, byte replaying)
    {
        if(State.Phase == 1 && State.IsCardinalFirst == null)
        {
            if(command == 407 && p1 == 4562 && sourceId.TryGetBattleNpc(out var x))
            {
                if(Vector2.Distance(x.Position.ToVector2(), new(100, 86)) < 2)
                {
                    State.IsCardinalFirst ??= true;
                    PluginLog.Information($"检测到先出现十字方位分身");
                }
                if(Vector2.Distance(x.Position.ToVector2(), new(110, 90)) < 2)
                {
                    State.IsCardinalFirst ??= false;
                    PluginLog.Information($"检测到先出现斜方位分身");
                }
            }
        }
    }

    /// <summary>
    /// Phase list:
    /// 0 - mechanic not yet started
    /// 1 - mechanic just started, initial tethers
    /// 2 - twisted vision 1 cast starts, nothing happens
    /// 3 - twisted vision 1 cast ends
    /// 4 - twisted vision 2 cast starts, first set of aoe previews starts
    /// 5 - twisted vision 2 cast ends, now must pick up your defamations and stacks
    /// 6 - twisted vision 3 cast starts, begin showing preview aoe
    /// 7 - twisted vision 3 cast ends, meteor must be resolved
    /// 8 - twisted vision 4 cast starts
    /// 9 - twisted vision 4 cast ends, must now resolve defamations and stacks
    /// 10 - twisted vision 5 cast starts, must show tower aoes
    /// 11 - twisted vision 5 cast ends, must keep showing tower aoes for a bit and then show position for debuffs
    /// 12 - twisted vision 6 cast starts, must prepare for defamations and stacks, 2x sets, must store aoes
    /// 13 - twisted vision 6 cast ends, must now show defamations and stacks, 1x set
    /// 14 - twisted vision 7 cast starts, must show safe platform and cones
    /// 15 - twisted vision 7 cast ends, must show safe platform and cones
    /// 16 - twisted vision 8 cast starts, must show portal clone. Portal clone always appears static north and does opposite set of cleaves than one on two safe platforms in the air.
    /// 17 - twisted vision 8 cast ends. Must show remaining defamations and stacks. Shortly after must show cleaves. Mechanic resolved.
    /// 
    /// How do clones work?
    /// In the beginning, they seem to appear on either cardinals or intercardinals first. 
    /// This determines which of the clone groups will fire their casts in first rewind, and which in second rewind. Cardinal rewinds first, intercardinal second.
    /// We make pairs out of players in a way that these first 4 clockwise always take stacks, and last 4 clockwise take defamations, clockwise from A. Why are we doing it like that though?
    /// The thing is, order in which they spawned will determine order in which they will respawn. Since we assigned first 4 clockwise players to take stacks, and second 4 players to take defamations, this effectively means that clones will be forced to behave a way that we want: defamations will spawn at west side and stacks and east side, making this mechanic easy to resolve. 
    /// How to programmatically resolve this?
    /// 1) Store which player associates with which clone at tether phase, as well as store clones positions
    /// 2) When players pick tethers up, associate their clone with certain mechanic. 
    /// 3) When clones respawn, draw mechanics on their positions
    /// Because script will remember which clones do what, plugin will draw correct AOEs for any strat.
    /// That is for second clone respawn.
    /// As for first clone respawn: to figure out which player does what, we need to know what tether appears at north. We already store enough information to figure that out. If it's defamation, then order will be defamation, stack, defamation, stack. And if it's stack, then it's stack, defamation, stack, defamation. It's always players 1+5, 2+6, 3+7, 4+8, they do their mechanics in pairs at the same time. 
    /// </summary>
    public override void OnUpdate()
    {
        Controller.Hide();
        if(State.Phase == 1)
        {
            var cloneCnt = AllClones.Count();
            if(cloneCnt != State.NumVisibleClones)
            {
                State.NumVisibleClones = cloneCnt;
                PluginLog.Information($"Clones count is now: {cloneCnt}");
            }


            var clones = MathHelper.EnumerateObjectsClockwise(Svc.Objects.OfType<IBattleNpc>().Where(x => x.DataId == (uint)DataIds.PlayerClone && x.Struct()->Vfx.Tethers.ToArray().Any(t => t.TargetId.ObjectId.EqualsAny(Controller.GetPartyMembers().Select(a => a.ObjectId)))), x => x.Position.ToVector2(), new(100, 100), new(96, 86));
            if(clones.Count == 8)
            {
                for(int i = 0; i < clones.Count; i++)
                {
                    IBattleNpc? x = clones[i];
                    if(x.Struct()->Vfx.Tethers.ToArray().Any(t => t.TargetId.ObjectId == BasePlayer.ObjectId))
                    {
                        State.PlayerPosition = i;
                        //PluginLog.Information($"Determined player position: {i + 1}");
                    }
                    if(x.Struct()->Vfx.Tethers.ToArray().TryGetFirst(x => x.TargetId.ObjectId.EqualsAny(Controller.GetPartyMembers().Select(s => s.ObjectId)), out var tetheredPlayer))
                    {
                        State.ClonePositions[tetheredPlayer.TargetId.ObjectId] = x.Position;
                    }
                }
            }
            State.NextCleavesList.Clear();
        }
        if(State.Phase == 2)
        {
            foreach(var x in Svc.Objects.OfType<IBattleNpc>())
            {
                if(x.IsCasting(46354) && x.CurrentCastTime.InRange(1, 2)) //cone aoes
                {
                    State.NextCleavesList.Add((x.Position, x.Rotation));
                }
                if(x.IsCasting(46353) && x.CurrentCastTime.InRange(1, 2))
                {
                    State.NextAOE = x.Position;
                }
            }
        }
        if(State.Phase == 4)
        {
            foreach(var x in Svc.Objects.OfType<IBattleNpc>())
            {
                if(x.IsCasting(46354) && x.CurrentCastTime > 1)
                {
                    State.NextCleavesList.Add((x.Position, x.Rotation));
                }
                if(x.IsCasting(46353))
                {
                    State.NextAOE = x.Position;
                }
            }
        }
        if(State.Phase == 5 || State.Phase == 6)
        {
            var clones = GetBossClones();
            if(clones.Count == 8)
            {
                var playersSupposedPickup = C.Pickups[State.PlayerPosition];
                var playerPickupsDefamation = ((int)playersSupposedPickup).InRange(0, 3, true);
                var playerPickupOrder = playerPickupsDefamation ? (int)playersSupposedPickup : (int)playersSupposedPickup - 4;
                var defaClone = 0;
                var stackClone = 0;
                for(int i = 0; i < clones.Count; i++)
                {
                    IBattleNpc? x = clones[i];
                    if(x.Struct()->Vfx.Tethers.ToArray().TryGetFirst(x => x.TargetId.ObjectId.EqualsAny(Controller.GetPartyMembers().Select(s => s.ObjectId)), out var tether))
                    {
                        var tetherTargetPosition = tether.TargetId.ObjectId.TryGetPlayer(out var tetherTarget)
                            ? tetherTarget.Position
                            : BasePlayer.Position;
                        if(x.Struct()->Vfx.Tethers.ToArray().TryGetFirst(x => x.TargetId.ObjectId.EqualsAny(Controller.GetPartyMembers().Select(s => s.ObjectId)), out var tetheredPlayer))
                        {
                            State.PlayerOrder[tetheredPlayer.TargetId.ObjectId] = i;
                        }
                        if(tether.Id == (uint)TetherKind.Defamation)
                        {
                            State.DefamationPlayers[tether.TargetId.ObjectId] = true;
                            if(playerPickupsDefamation && defaClone == playerPickupOrder && Controller.TryGetElementByName("PickTether", out var e))
                            {
                                if(!C.SkipIndiMechs)
                                {
                                    e.Enabled = C.ShowTetherLine;
                                }
                                e.color = GetRainbowColor(1f).ToUint();
                                e.SetRefPosition(x.Position);
                                e.SetOffPosition(tetherTargetPosition);
                                if(Controller.TryGetElementByName("PickTetherCircle", out var e2))
                                {
                                    if(!C.SkipIndiMechs)
                                    {
                                        e2.Enabled = C.ShowTetherCircle;
                                    }
                                    e2.refActorObjectID = x.ObjectId;
                                }
                            }
                            defaClone++;
                        }
                        if(tether.Id == (uint)TetherKind.Stack)
                        {
                            State.DefamationPlayers[tether.TargetId.ObjectId] = false;
                            if(!playerPickupsDefamation && stackClone == playerPickupOrder && Controller.TryGetElementByName("PickTether", out var e))
                            {
                                if(!C.SkipIndiMechs)
                                {
                                    e.Enabled = true;
                                }
                                e.color = GetRainbowColor(1f).ToUint();
                                e.SetRefPosition(x.Position);
                                e.SetOffPosition(tetherTargetPosition);
                                if(Controller.TryGetElementByName("PickTetherCircle", out var e2))
                                {
                                    if(!C.SkipIndiMechs)
                                    {
                                        e2.Enabled = C.ShowTetherCircle;
                                    }
                                    e2.refActorObjectID = x.ObjectId;
                                }
                            }
                            stackClone++;
                        }
                    }
                }
            }
        }

        if(State.Phase == 6 || State.Phase == 7 || State.Phase == 8)
        {
            int i = 0;
            foreach(var x in State.NextCleavesList)
            {
                i++;
                if(Controller.TryGetElementByName($"Cone{i}", out var e))
                {
                    e.Enabled = true;
                    e.AdditionalRotation = x.Rot;
                    e.fillIntensity = State.Phase == 6 ? 0.2f : 0.5f;
                    e.SetRefPosition(x.Pos);
                }
            }

            {
                if(State.NextAOE != null && Controller.TryGetElementByName($"Circle", out var e))
                {
                    e.Enabled = true;
                    e.fillIntensity = State.Phase == 6 ? 0.2f : 0.5f;
                    e.SetRefPosition(State.NextAOE.Value);
                }
            }
        }

        if (State.Phase == 7 && State.Phase7Sub == 0)
        {
            if (State.IsConeSafeNorth.HasValue)
            {
                {
                    if (Controller.TryGetElementByName("p7sub1 tether", out var e))
                    {
                        if (State.IsConeSafeNorth.Value)
                        {
                            if (C.IsGroup1) // West
                                e.SetRefPosition(new Vector3(90, 0, 90));
                            else
                                e.SetRefPosition(new Vector3(110, 0, 90));
                        }
                        else
                        {
                            if (C.IsGroup1) // West
                                e.SetRefPosition(new Vector3(90, 0, 110));
                            else
                                e.SetRefPosition(new Vector3(110, 0, 110));
                        }

                        e.color = GetRainbowColor(1f).ToUint();
                        e.Enabled = true;
                    }
                }
            }
        }

        if (State.Phase == 7 && State.Phase7Sub == 1)
        {
            var baseMeleePos = new Vector3(90.243f, 0f, 95.757f); // West Melee Left
            var baseRangedPos = new Vector3(81.757f, 0f, 95.757f); // West Ranged Left
            var pos = C.TowerPosition switch
            {
                TowerPosition.MeleeLeft => baseMeleePos,
                TowerPosition.MeleeRight => baseMeleePos with { Z = 200f - baseMeleePos.Z, },
                TowerPosition.RangedLeft => baseRangedPos,
                TowerPosition.RangedRight => baseRangedPos with { Z = 200f - baseRangedPos.Z, },
                _ => throw new Exception("无效塔位置"),
            };

            pos = C.IsGroup1 ? pos : pos with { X = 200f - pos.X, Z = 200f - pos.Z, };
            {
                {
                    if (Controller.TryGetElementByName("TowerTether", out var e))
                    {
                        e.radius = 3f;
                        e.color = GetRainbowColor(1f).ToUint();
                        e.SetRefPosition(pos);
                        e.Enabled = true;
                    }
                }
            }

            {
                if (Controller.TryGetElementByName("P7AOERadius", out var e)) e.Enabled = true;
            }
        }

        if(State.Phase == 9 && GetAdjustedDefamationNumber() < 4)
        {
            var playerGroup2 = State.PlayerOrder.FindKeysByValue(0 + 1 * GetAdjustedDefamationNumber()).FirstOrDefault().GetObject();
            var playerGroup1 = State.PlayerOrder.FindKeysByValue(4 + 1 * GetAdjustedDefamationNumber()).FirstOrDefault().GetObject();
            var isDefamationPlayerGroup2 = State.DefamationPlayers[playerGroup2.ObjectId];
            var isDefamationPlayerGroup1 = State.DefamationPlayers[playerGroup1.ObjectId];
            //var party = this.PlayerOrder.OrderBy(x => x.Value).Take(4).Any(x => x.Key == BasePlayer.ObjectId) ? 2 : 1;
            var playersDirection = (Direction)State.PlayerOrder[BasePlayer.ObjectId];
            var party = (State.DefamationPlayers[State.PlayerOrder.FindKeysByValue(0).First()] ? C.LP2CardinalDefamationFirst : C.LP2CardinalStackFirst).Contains(playersDirection) ? 2 : 1;
            if((playerGroup2.AddressEquals(BasePlayer) && isDefamationPlayerGroup2) || (playerGroup1.ObjectId == BasePlayer.ObjectId && isDefamationPlayerGroup1))
            {
                if(Controller.TryGetElementByName($"DefamationOnYou", out var e))
                {
                    e.Enabled = true;
                }
            }
            if(isDefamationPlayerGroup2 && !Controller.GetElementByName("DefamationOnYou")!.Enabled)
            {
                if(Controller.TryGetElementByName($"SafespotGroup{party}", out var e))
                {
                    e.Enabled = !C.SkipIndiMechs;
                    e.color = GetRainbowColor(1f).ToUint();
                }
            }
            {
                if(isDefamationPlayerGroup2 && Controller.TryGetElementByName($"Defamation2", out var e))
                {
                    e.Enabled = true;
                    e.SetRefPosition(playerGroup2.Position);
                    if(Controller.GetElementByName("DefamationOnYou")!.Enabled && party == 2 && Controller.TryGetElementByName("DefamationGroup2", out var el))
                    {
                        el.Enabled = !C.SkipIndiMechs;
                        el.color = GetRainbowColor(1f).ToUint();
                    }
                }
            }
            {
                if(isDefamationPlayerGroup1 && Controller.TryGetElementByName($"Defamation1", out var e))
                {
                    e.Enabled = true;
                    e.SetRefPosition(playerGroup1.Position);
                    if(Controller.GetElementByName("DefamationOnYou")!.Enabled && party == 1 && Controller.TryGetElementByName("DefamationGroup1", out var el))
                    {
                        el.Enabled = !C.SkipIndiMechs;
                        el.color = GetRainbowColor(1f).ToUint();
                    }
                }
            }
            {
                if(!isDefamationPlayerGroup2 && Controller.TryGetElementByName($"Stack2", out var e))
                {
                    e.Enabled = true;
                    e.SetRefPosition(playerGroup2.Position);
                    if(party == 2 && Controller.TryGetElementByName("StackGroup2", out var el))
                    {
                        el.Enabled = !C.SkipIndiMechs;
                        el.color = GetRainbowColor(1f).ToUint();
                    }
                }
            }
            {
                if(!isDefamationPlayerGroup1 && Controller.TryGetElementByName($"Stack1", out var e))
                {
                    e.Enabled = true;
                    e.SetRefPosition(playerGroup1.Position);
                    if(party == 1 && Controller.TryGetElementByName("StackGroup1", out var el))
                    {
                        el.Enabled = !C.SkipIndiMechs;
                        el.color = GetRainbowColor(1f).ToUint();
                    }
                }
            }
        }

        if (State.Phase is 10 or 11)
        {
            if (State.Phase11Sub == 0) // tower goes
            {
                var tower = GetShouldTakeTower();
                // Get tower kind
                if (tower != null)
                {
                    if (Controller.TryGetElementByName("TowerTether", out var e))
                    {
                        var isMelee = C.TowerPosition is TowerPosition.MeleeRight or TowerPosition.MeleeLeft;
                        var nameId = tower.Struct()->GetNameId();
                        if (Svc.Condition[ConditionFlag.DutyRecorderPlayback] && State.DebugOverWriteTower.HasValue)
                            nameId = (uint)State.DebugOverWriteTower.Value;

                        // if Wind, offset position a bit
                        if (nameId is (uint)Towers.DoomLight)
                        {
                            e.radius = 0.35f;
                            if (isMelee)
                            {
                                if (tower.Position.Z > 100)
                                    e.SetRefPosition(tower.Position + new Vector3(0, 0, 1.5f));
                                else
                                    e.SetRefPosition(tower.Position + new Vector3(0, 0, -1.5f));
                            }
                            else
                            {
                                if (tower.Position.X > 100)
                                    e.SetRefPosition(tower.Position + new Vector3(1.5f, 0, 0));
                                else
                                    e.SetRefPosition(tower.Position + new Vector3(-1.5f, 0, 0));
                            }
                        }
                        else if (nameId is (uint)Towers.WindLight)
                        {
                            e.radius = 0.35f;
                            if (tower.Position.X > 100)
                                e.SetRefPosition(tower.Position + new Vector3(-1.5f, 0, 0));
                            else
                                e.SetRefPosition(tower.Position + new Vector3(1.5f, 0, 0));
                        }
                        else
                        {
                            e.radius = 3f;
                            e.SetRefPosition(tower.Position);
                        }

                        e.Enabled = true;
                        e.color = GetRainbowColor(1f).ToUint();
                    }
                }
            }

            if (State.Phase11Sub == 1) // wait for tower effects
            {
                // Fire
                if (BasePlayer.StatusList.Any(y => y.StatusId == 4768))
                {
                    {
                        if (Controller.TryGetElementByName("TowerTether", out var e))
                        {
                            e.SetRefPosition(BasePlayer.Position);
                            e.color = GetRainbowColor(1f).ToUint();
                            e.radius = 0.35f;
                            e.tether = true;
                            e.thicc = 5f;
                            e.Enabled = true;
                        }
                    }
                }

                // Earth
                var earthTowers = State.TowerDataArray.Where(x => x.kinds == Towers.Earth);
                for (var i = 0; i < earthTowers.Count(); i++)
                {
                    var tower = earthTowers.ElementAt(i);
                    {
                        if (Controller.TryGetElementByName($"Rock{i + 1}", out var e))
                        {
                            e.SetRefPosition(tower.Position);
                            e.Enabled = true;
                        }
                    }
                }
            }

            if (State.Phase11Sub == 2 && !C.DontShowElementsP11S1) // cone goes
            {
                var pcs = Svc.Objects.OfType<IPlayerCharacter>().ToList();

                var farBuffer = pcs.Where(x => x.StatusList.Any(y => y.StatusId == 4766)).ToList();
                var nearBuffer = pcs.Where(x => x.StatusList.Any(y => y.StatusId == 4767)).ToList();

                if (farBuffer.Count + nearBuffer.Count == 4)
                {
                    for (var i = 0; i < farBuffer.Count; i++)
                    {
                        var buffer = farBuffer[i];
                        // Find the farthest object in pcs
                        var farthest = pcs.OrderByDescending(x =>
                            Vector3.DistanceSquared(x.Position, buffer.Position)).FirstOrDefault();
                        if (farthest != null)
                        {
                            if (Controller.TryGetElementByName($"FarCone{i + 1}", out var e))
                            {
                                e.refActorComparisonType = 2;
                                e.refActorObjectID = buffer.EntityId;
                                e.faceplayer = GetPlayerOrder(farthest);
                                e.Enabled = true;
                            }
                        }
                    }

                    for (var i = 0; i < nearBuffer.Count; i++)
                    {
                        var buffer = nearBuffer[i];
                        // Find the nearest object in pcs
                        var nearest = pcs.OrderBy(x =>
                            Vector3.Distance(x.Position, buffer.Position)).Skip(1).FirstOrDefault();
                        if (nearest != null)
                        {
                            {
                                if (Controller.TryGetElementByName($"NearCone{i + 1}", out var e))
                                {
                                    e.refActorComparisonType = 2;
                                    e.refActorObjectID = buffer.EntityId;
                                    e.faceplayer = GetPlayerOrder(nearest);
                                    e.Enabled = true;
                                }
                            }
                        }
                    }

                    if (!C.TakenCheckConditionIsTakenTower) // base role
                    {
                        var isMelee = C.TowerPosition is TowerPosition.MeleeRight or TowerPosition.MeleeLeft;
                        var elementName = (C.TakenFarIsMelee, isMelee) switch
                        {
                            (true, true) => "Taken Far",
                            (true, false) => "Taken Near",
                            (false, true) => "Taken Near",
                            (false, false) => "Taken Far",
                        };

                        // Has Far
                        if (BasePlayer.StatusList.Any(y => y.StatusId == 4766)) elementName = "Given Far";
                        else if (BasePlayer.StatusList.Any(y => y.StatusId == 4767)) elementName = "Given Near";
                        {
                            if (Controller.TryGetElementByName(elementName, out var e))
                            {
                                if ((BasePlayer.Position.X > 100 && e.refX < 100) ||
                                    (BasePlayer.Position.X < 100 && e.refX > 100))
                                    e.refX = 200 - e.refX; // mirror
                                e.color = GetRainbowColor(1f).ToUint();
                                e.tether = true;
                                e.thicc = 5f;
                                e.Enabled = true;
                            }
                        }
                    }
                    else // base taken tower
                    {
                        var earthPlayerId = C.IsGroup1 ?
                            State.TowerDataArray.FirstOrDefault(x => x is { kinds: Towers.Earth, Side: Direction.W })?.AssignToPlayerEntityId : // West
                            State.TowerDataArray.FirstOrDefault(x => x is { kinds: Towers.Earth, Side: Direction.E })?.AssignToPlayerEntityId;  // East
                        var firePlayerId = C.IsGroup1 ?
                            State.TowerDataArray.FirstOrDefault(x => x is { kinds: Towers.Fire, Side: Direction.W })?.AssignToPlayerEntityId : // West
                            State.TowerDataArray.FirstOrDefault(x => x is { kinds: Towers.Fire, Side: Direction.E })?.AssignToPlayerEntityId;  // East
                        if (earthPlayerId != null && firePlayerId != null)
                        {
                            var isEarth = BasePlayer.ObjectId == earthPlayerId;
                            var elementName = (isEarth && C.TakenFarIsEarth) || (!isEarth && !C.TakenFarIsEarth)
                                ? "Taken Far"
                                : "Taken Near";
                            // Has Far
                            if (isEarth && BasePlayer.StatusList.Any(y => y.StatusId == 4767)) elementName = "Given Near";
                            else if (!isEarth && BasePlayer.StatusList.Any(y => y.StatusId == 4766)) elementName = "Given Far";
                            
                            // Has Far
                            if (BasePlayer.StatusList.Any(y => y.StatusId == 4766)) elementName = "Given Far";
                            else if (BasePlayer.StatusList.Any(y => y.StatusId == 4767)) elementName = "Given Near";

                            if(Controller.TryGetElementByName(elementName, out var e))
                            {
                                if((BasePlayer.Position.X > 100 && e.refX < 100) ||
                                    (BasePlayer.Position.X < 100 && e.refX > 100))
                                    e.refX = 200 - e.refX; // mirror
                                e.color = GetRainbowColor(1f).ToUint();
                                e.tether = true;
                                e.thicc = 5f;
                                e.Enabled = true;
                            }
                        }
                    }

                    string GetPlayerOrder(IPlayerCharacter c)
                    {
                        for (var i = 1; i <= 8; i++)
                            if ((nint)FakePronoun.Resolve($"<{i}>") == c.Address)
                                return $"<{i}>";
                        throw new Exception("无法确定玩家顺序");
                    }
                }
            }
            
            IGameObject? GetShouldTakeTower()
            {
                var nonLightTowers =
                    Svc.Objects.Where(x => x.Struct()->GetNameId() is (uint)Towers.Fire or (uint)Towers.Earth);
                var assignedNonLightTowers = C.IsGroup1
                    ? nonLightTowers.Where(x => x.Position.X < 100)
                    : nonLightTowers.Where(x => x.Position.X > 100);
                var lightTowers = Svc.Objects.Where(x =>
                    x.Struct()->GetNameId() is (uint)Towers.WindLight or (uint)Towers.DoomLight);
                var assignedLightTowers = C.IsGroup1
                    ? lightTowers.Where(x => x.Position.X < 100)
                    : lightTowers.Where(x => x.Position.X > 100);

                State.TowersDebug =
                [
                    ("Assigned Non-Light Towers", assignedNonLightTowers.FirstOrDefault()?.EntityId ?? 0),
                    ("Assigned Non-Light Towers", assignedNonLightTowers.Skip(1).FirstOrDefault()?.EntityId ?? 0),
                    ("Assigned Light Towers", assignedLightTowers.FirstOrDefault()?.EntityId ?? 0),
                    ("Assigned Light Towers", assignedLightTowers.Skip(1).FirstOrDefault() ?.EntityId ?? 0),
                ];

                if (assignedNonLightTowers.Count() + assignedLightTowers.Count() != 4)
                    throw new Exception("分配的非法数量非轻型塔");

                var isDps = BasePlayer.GetRole() == CombatRole.DPS;
                if (!State.IsThDecreasingResistance.HasValue) throw new Exception("未设置DPS");
                var canTakingLightTowers = isDps == State.IsThDecreasingResistance;
                var isMelee = C.TowerPosition is TowerPosition.MeleeRight or TowerPosition.MeleeLeft;
                return (isMelee, canTakingLightTowers) switch
                {
                    // Melee(The tower closest to the coordinates 100, 0, 100)
                    // Fire/Earth
                    (true, false) => assignedNonLightTowers
                        .OrderBy(x => Vector2.Distance(x.Position.ToVector2(), new Vector2(100, 100))).FirstOrDefault(),
                    // Wind/Doom
                    (true, true) => assignedLightTowers
                        .OrderBy(x => Vector2.Distance(x.Position.ToVector2(), new Vector2(100, 100))).FirstOrDefault(),
                    // Ranged(The tower farthest from the coordinates 100, 0, 100)
                    // Fire/Earth
                    (false, false) => assignedNonLightTowers
                        .OrderByDescending(x => Vector2.Distance(x.Position.ToVector2(), new Vector2(100, 100)))
                        .FirstOrDefault(),
                    // Wind/Doom
                    (false, true) => assignedLightTowers
                        .OrderByDescending(x => Vector2.Distance(x.Position.ToVector2(), new Vector2(100, 100)))
                        .FirstOrDefault(),
                };
            }
        }

        void processStored(int num)
        {
            var orderedClones = MathHelper.EnumerateObjectsClockwise(State.ClonePositions, x => x.Value.ToVector2(), new(100, 100), new(98, 86));
            var player1 = orderedClones[num].Key.GetObject();
            var isDefamationPlayer1 = State.DefamationPlayers[player1.ObjectId];
            if(Controller.GetRegisteredElements().TryGetFirst(x => !x.Value.Enabled && x.Key.EqualsAny(isDefamationPlayer1 ? ["Defamation1", "Defamation2"] : ["Stack1","Stack2"]), out var e))
            {
                e.Value.Enabled = true;
                e.Value.SetRefPosition(orderedClones[num].Value);
            }
        }

        if(State.Phase == 12)
        {
            foreach(var x in Svc.Objects.OfType<IBattleNpc>())
            {
                if(x.IsCasting(46352) && x.CurrentCastTime.InRange(1, 2)) //cone aoes n/s
                {
                    State.NextCleavesList.Add((x.Position, 0.DegreesToRadians()));
                    State.NextCleavesList.Add((x.Position, 180.DegreesToRadians()));
                    State.NextCleavesNorthSouth = false;
                }
                if(x.IsCasting(46351) && x.CurrentCastTime.InRange(1, 2)) //cone aoes e/w
                {
                    State.NextCleavesList.Add((x.Position, 90.DegreesToRadians()));
                    State.NextCleavesList.Add((x.Position, 270.DegreesToRadians()));
                    State.NextCleavesNorthSouth = true;
                }
                if(x.IsCasting(48303) && x.CurrentCastTime.InRange(1, 2))
                {
                    State.NextAOE = x.Position;
                }
            }
        }

        if(State.Phase == 13 || State.Phase == 14)
        {
            if(GetAdjustedDefamationNumber() < 5)
            {
                if(State.IsCardinalFirst == true)
                {
                    processStored(0);
                    processStored(2);
                    processStored(4);
                    processStored(6);
                }
                else if(State.IsCardinalFirst == false)
                {
                    processStored(1);
                    processStored(3);
                    processStored(5);
                    processStored(7);
                }
            }
        }

        if(State.Phase == 16 || State.Phase == 17)
        {
            if(GetAdjustedDefamationNumber() < 6)
            {
                if(State.IsCardinalFirst == true)
                {
                    processStored(1);
                    processStored(3);
                    processStored(5);
                    processStored(7);
                }
                else if(State.IsCardinalFirst == false)
                {
                    processStored(0);
                    processStored(2);
                    processStored(4);
                    processStored(6);
                }
            }
        }

        if ((State.Phase == 13 && GetAdjustedDefamationNumber() < 5) || (State.Phase.EqualsAny(16, 17) && GetAdjustedDefamationNumber() < 6))
        {
            Vector3? finalPosition = null;
            Vector3 getPosition(string element)
            {
                var e = Controller.GetElementByName(element);
                return new(e?.refX ?? 0, e?.refZ ?? 0, e?.refY ?? 0);
            }
            List<Vector3> stackPos = [getPosition("Stack1"), getPosition("Stack2")];

            if(C.AltCloneResolution)
            {
                var position = stackPos.FirstOrDefault(x => C.AltCloneDirections.Any(a => Vector2.Distance(x.ToVector2(), this.ReenactmentDirections[a]) < 2));
                if(position != default)
                {
                    finalPosition = position;
                }
            }
            else
            {
                if(C.StackEnumPrioHorizontal)
                {
                    if(stackPos[0].X.ApproximatelyEquals(stackPos[1].X, 1)) //horizontally equal
                    {
                        //apply vertical prio
                        stackPos = stackPos.OrderBy(x => x.Z).ToList();
                        finalPosition = stackPos[C.StackEnumVerticalNorth ? 0 : 1];
                    }
                    else
                    {
                        stackPos = stackPos.OrderBy(x => x.X).ToList();
                        finalPosition = stackPos[C.StackEnumHorizontalWest ? 0 : 1];
                    }
                }
                else
                {
                    if(stackPos[0].Z.ApproximatelyEquals(stackPos[1].Z, 1)) //vertically equal
                    {
                        //apply horizontal prio
                        stackPos = stackPos.OrderBy(x => x.X).ToList();
                        finalPosition = stackPos[C.StackEnumHorizontalWest ? 0 : 1];
                    }
                    else
                    {
                        stackPos = stackPos.OrderBy(x => x.Z).ToList();
                        finalPosition = stackPos[C.StackEnumVerticalNorth ? 0 : 1];
                    }
                }
            }

            if(finalPosition != null)
            {
                if(Controller.TryGetElementByName("stack tether", out var e))
                {
                    e.Enabled = !C.SkipIndiMechs;
                    e.color = GetRainbowColor(1f).ToUint();
                    e.SetRefPosition(finalPosition.Value);
                }
            }
        }

        if(State.Phase == 17)
        {
            if(State.NextCleavesNorthSouth == true)
            {
                {
                    if(Controller.TryGetElementByName("PortalConeNS1", out var e))
                    {
                        e.Enabled = true;
                        e.fillIntensity = GetAdjustedDefamationNumber() < 6 ? 0.2f : 0.5f;
                    }
                }
                {
                    if(Controller.TryGetElementByName("PortalConeNS2", out var e))
                    {
                        e.Enabled = true;
                        e.fillIntensity = GetAdjustedDefamationNumber() < 6 ? 0.2f : 0.5f;
                    }
                }
            }
            if(this.State.NextCleavesNorthSouth == false)
            {
                {
                    if(Controller.TryGetElementByName("PortalConeEW1", out var e))
                    {
                        e.Enabled = true;
                        e.fillIntensity = GetAdjustedDefamationNumber() < 6 ? 0.1f : 0.5f;
                    }
                }
                {
                    if(Controller.TryGetElementByName("PortalConeEW2", out var e))
                    {
                        e.Enabled = true;
                        e.fillIntensity = GetAdjustedDefamationNumber() < 6 ? 0.2f : 0.5f;
                    }
                }
            }
        }

        if(State.Phase == 12 || State.Phase == 13 || State.Phase == 14 || State.Phase == 15)
        {
            var eastUnsafe = State.NextCleavesList.Any(x => x.Pos.X < 100);
            Element? e = null;
            var mustGo = State.Phase > 13 || (State.Phase == 13 && GetAdjustedDefamationNumber() >= 5);
            if(State.NextCleavesNorthSouth == true)
            {
                e = Controller.GetElementByName($"Safe{(eastUnsafe ? 西 : 东)}LeftRight{(mustGo ? "A" : "")}");
                
            }
            if(State.NextCleavesNorthSouth == false)
            {
                e = Controller.GetElementByName($"Safe{(eastUnsafe ? 西 : 东)}FrontBack{(mustGo ? "A" : "")}");
            }
            e?.Enabled = true;
            if(mustGo) e?.color = GetRainbowColor(1f).ToUint();
        }

        if(State.Phase == 14 || State.Phase == 15 || State.Phase == 16)
        {
            int i = 0;
            foreach(var x in this.State.NextCleavesList)
            {
                i++;
                if(Controller.TryGetElementByName($"Cone{i}", out var e))
                {
                    e.Enabled = true;
                    e.AdditionalRotation = x.Rot;
                    e.fillIntensity = State.Phase == 14 ? 0.2f : 0.5f;
                    e.SetRefPosition(x.Pos);
                }
            }
            {
                if(this.State.NextAOE != null && Controller.TryGetElementByName($"Circle", out var e))
                {
                    e.Enabled = true;
                    e.fillIntensity = State.Phase == 14 ? 0.2f : 0.5f;
                    e.SetRefPosition(this.State.NextAOE.Value);
                }
            }
        }
    }

    private List<IBattleNpc> GetBossClones()
    {
        return MathHelper.EnumerateObjectsClockwise(Svc.Objects.OfType<IBattleNpc>().Where(x => x.NameId == 14380 && x.Struct()->Vfx.Tethers.ToArray().Any(t => t.TargetId.ObjectId.EqualsAny(Controller.GetPartyMembers().Select(a => a.ObjectId)))), x => x.Position.ToVector2(), new(100, 100), new(96, 86));
    }

    public override void OnActionEffectEvent(ActionEffectSet set)
    {
        if(set.Action?.RowId == 46345)
        {
            State.Phase = 1;
        }
        if(set.Action?.RowId == 48098)
        {
            State.Phase++;
        }
        if(set.Action?.RowId == 46358)
        {
            this.State.NextAOE = null;
            this.State.NextCleavesList.Clear();
            if(State.Phase == 17)
            {
                this.State.NextCleavesNorthSouth = null;
                Controller.Reset();
            }
        }
        if(State.Phase == 9 && set.Action?.RowId.EqualsAny<uint>(46360, 46361) == true)
        {
            State.DefamationAttack++;
        }
        if(State.Phase.EqualsAny(13,14,16,17) && set.Action?.RowId.EqualsAny<uint>(48099) == true)
        {
            State.DefamationAttack++;
        }
        if (State.Phase == 7 && State.Phase7Sub == 0 && set.Action?.RowId == 46356) State.Phase7Sub++;
        if (State.Phase == 7 && set.Action?.RowId == 46367)
        {
            Controller.Schedule(() =>
            {
                var tower = Svc.Objects.Where(x => x.Struct()->GetNameId() is (uint)Towers.Fire or (uint)Towers.Earth
                    or (uint)Towers.WindLight or (uint)Towers.DoomLight);

                foreach (var t in tower)
                {
                    var ew = t.Position.X > 100 ? Direction.E : Direction.W;
                    State.TowerDataArray.FirstOrDefault(x => x.Side == ew && (uint)x.kinds == t.Struct()->GetNameId())
                        ?.Position = t.Position;
                }
            }, 1000);
        }
        
        if (State.Phase is 10 or 11 && State.Phase11Sub == 1 && set.Action?.RowId == 46327) State.Phase11Sub++;
        if (State.Phase is 10 or 11 && State.Phase11Sub == 2 && set.Action?.RowId == 46330) State.Phase11Sub++;
        if (State.Phase is 10 or 11 && set.Action?.RowId == 46324)
        {
            var tower = State.TowerDataArray.FirstOrDefault(x => Vector2.Distance(x.Position.ToVector2(), set.Source.Position.ToVector2()) < 2);
            if (tower == null)
            {
                PluginLog.Error("塔数据数组为空");
                return;
            }
            var pc = Svc.Objects.OfType<IPlayerCharacter>().FirstOrDefault(x => Vector2.Distance(x.Position.ToVector2(), set.Source.Position.ToVector2()) < 2);
            if (pc == null)
            {
                PluginLog.Error("无法找到塔附近的玩家角色");
                return;
            }
            tower.AssignToPlayerEntityId = pc.EntityId;
        }
    }

    public override void OnGainBuffEffect(uint sourceId, Status Status)
    {
        if (State.Phase == 7 && Status.StatusId == 4164 && !State.IsThDecreasingResistance.HasValue) // light tower debuff
        {
            if (sourceId.TryGetPlayer(out var pc))
                State.IsThDecreasingResistance = pc.GetRole() != CombatRole.DPS;
        }

        if (State.Phase is 10 or 11 && State.Phase11Sub == 0 && Status.StatusId is 4766 or 4767) State.Phase11Sub++;
    }

    int GetAdjustedDefamationNumber()
    {
        return State.DefamationAttack / 2;
    }

    public override unsafe void OnStartingCast(uint sourceId, PacketActorCast* packet)
    {
        if(packet->ActionDescriptor == new ActionDescriptor(ActionType.Action, 48098))
        {
            State.Phase++;
        }
        if (packet->ActionDescriptor == new ActionDescriptor(ActionType.Action, 46352) && State.Phase is 3 or 4)
        {
            if (packet->Position.Z < 100)
                State.IsConeSafeNorth = true;
            else
                State.IsConeSafeNorth = false;
        }
    }

    ImGuiEx.RealtimeDragDrop<PickupOrder> PickupDrag = new("DePiOrd", x => x switch
    {
        PickupOrder.Defamation_1 => "诽谤_1",
        PickupOrder.Defamation_2 => "诽谤_2",
        PickupOrder.Defamation_3 => "诽谤_3",
        PickupOrder.Defamation_4 => "诽谤_4",
        PickupOrder.Stack_1 => "分摊_1",
        PickupOrder.Stack_2 => "分摊_2",
        PickupOrder.Stack_3 => "分摊_3",
        PickupOrder.Stack_4 => "分摊_4",
        _ => x.ToString(),
    });
    public override void OnSettingsDraw()
    {
        ImGui.Checkbox("禁用彩虹颜色", ref C.NoRainbow);
        if(C.NoRainbow)
        {
            ImGui.ColorEdit4("替代颜色", ref C.FixedColor, ImGuiColorEditFlags.NoInputs);
        }
        ImGuiEx.Checkbox("不解析单独机制", ref C.SkipIndiMechs);
        ImGuiEx.HelpMarker("如果启用此模式，单独机制将不会被解析。插件仍会显示分摊和诽谤的AOE以及存储的AOE，但不会指向你的连线，也不会显示你的分摊/散开位置，也不会显示塔的分配。");
        if(C.SkipIndiMechs)
        {
            ImGuiEx.Checkbox("不显示塔的debuff(扇形和连线)", ref C.DontShowElementsP11S1);
        }
        else 
        {
            ImGuiEx.Text($"本策略说明:");
            ImGuiEx.InputTextMultilineExpanding("##desc", ref C.Comment, 2000, 2, 40);
            ImGuiEx.TextWrapped(EColor.OrangeBright, "默认基于疲惫指南的顶球处理诽谤/分摊配置。如需适配其他攻略，请前往 Registered Elements 标签页修改位置，本脚本可适配大多数已知打法。");
            ImGui.Separator();
            ImGuiEx.Text(EColor.YellowBright, "连线:");
            ImGui.Indent();
            ImGuiEx.TextV($"可视化建议连线:");
            ImGui.SameLine();
            ImGui.Checkbox("线条显示", ref C.ShowTetherLine);
            ImGui.SameLine();
            ImGui.Checkbox("圆形显示", ref C.ShowTetherCircle);
            ImGuiEx.Text($"连线拾取顺序，从北开始顺时针:");
            ImGui.Indent();
            PickupDrag.Begin();
            if(ImGuiEx.BeginDefaultTable("DefaPickup", ["##reorder", "连线类型和顺序", "连线拾取方向", "优先分摊的LP", "优先诽谤的LP"]))
            {
                for(int i = 0; i < C.Pickups.Count; i++)
                {
                    ImGui.TableNextRow();
                    PickupOrder x = C.Pickups[i];
                    PickupDrag.SetRowColor(x);
                    ImGui.TableNextColumn();
                    PickupDrag.DrawButtonDummy(x switch {
                        PickupOrder.Defamation_1 => "诽谤_1",
                        PickupOrder.Defamation_2 => "诽谤_2",
                        PickupOrder.Defamation_3 => "诽谤_3",
                        PickupOrder.Defamation_4 => "诽谤_4",
                        PickupOrder.Stack_1 => "分摊_1",
                        PickupOrder.Stack_2 => "分摊_2",
                        PickupOrder.Stack_3 => "分摊_3",
                        PickupOrder.Stack_4 => "分摊_4",
                        _ => x.ToString(),
                    }, C.Pickups, i);
                    ImGui.TableNextColumn();
                    ImGuiEx.TextV(x switch {
                        PickupOrder.Defamation_1 => "诽谤_1",
                        PickupOrder.Defamation_2 => "诽谤_2",
                        PickupOrder.Defamation_3 => "诽谤_3",
                        PickupOrder.Defamation_4 => "诽谤_4",
                        PickupOrder.Stack_1 => "分摊_1",
                        PickupOrder.Stack_2 => "分摊_2",
                        PickupOrder.Stack_3 => "分摊_3",
                        PickupOrder.Stack_4 => "分摊_4",
                        _ => x.ToString(),
                    });
                    ImGui.TableNextColumn();
                    ImGuiEx.TextV(ImGuiColors.DalamudGrey, $"{(Direction)i}");
                    ImGui.TableNextColumn();
                    ImGuiEx.TextV(ImGuiColors.DalamudGrey, C.LP2CardinalStackFirst.Contains((Direction)i) ? "队伍2" : "队伍1");
                    ImGui.TableNextColumn();
                    if(C.LP2CardinalStackFirst.Contains((Direction)i) != C.LP2CardinalDefamationFirst.Contains((Direction)i))
                    {
                        ImGuiEx.TextV(ImGuiColors.DalamudGrey, C.LP2CardinalDefamationFirst.Contains((Direction)i) ? "队伍2" : "队伍1");
                    }
                    else
                    {
                        ImGuiEx.TextV(ImGuiColors.DalamudGrey3, $"与分摊相同");
                    }
                }
                ImGui.EndTable();
            }
            PickupDrag.End();
            ImGui.Unindent();

            void defaStackAssignLp(string which, HashSet<Direction> collection)
            {
                ImGui.PushID(which);
                ImGuiEx.Text(EColor.YellowBright, which == "Stack" ? "分摊连线在十字方位时的光之队分配:" : "诽谤连线在十字方位时的光之队分配:");
                ImGuiEx.HelpMarker($"此设置定义在常规机制处理中，哪些玩家属于哪个光之队来放置诽谤和分摊。这些分配用于当BOSS分身的连线生成在十字方位时。这不影响回放机制。这些光之队分配基于玩家拾取的BOSS分身连线，而非初始玩家分身。");
                ImGui.Indent();
                if(collection.Count == 4)
                {
                    ImGuiEx.Text(EColor.GreenBright, "配置看起来有效");
                }
                else
                {
                    ImGuiEx.Text(EColor.RedBright, "配置无效，每个光之队必须包含4名玩家");
                }
                if(ImGuiEx.BeginDefaultTable("LpStackAssign", ["方向", "~分配"], false))
                {
                    for(int i = 0; i < Enum.GetValues<Direction>().Length; i++)
                    {
                        var item = Enum.GetValues<Direction>()[i];
                        ImGui.PushID(item.ToString());
                        ImGui.TableNextRow();
                        ImGui.TableNextColumn();
                        ImGuiEx.TextV($"{item}");
                        ImGui.TableNextColumn();
                        if(ImGui.RadioButton("光之队1", !collection.Contains(item))) collection.Remove(item);
                        ImGui.SameLine();
                        if(ImGui.RadioButton("光之队2", collection.Contains(item))) collection.Add(item);
                        ImGui.PopID();
                    }
                    ImGui.EndTable();
                }
                ImGui.Unindent();
                ImGui.PopID();
            }
            defaStackAssignLp("Stack", C.LP2CardinalStackFirst);
            defaStackAssignLp("Defamation", C.LP2CardinalDefamationFirst);

            ImGui.Unindent();

            ImGuiEx.Text(EColor.YellowBright, "塔:");
            ImGui.Indent();
            ImGuiEx.Checkbox("不显示塔的debuff(扇形和连线)", ref C.DontShowElementsP11S1);
            ImGui.SetNextItemWidth(150f);
            ImGuiEx.EnumCombo("我的塔位置，面对Boss", ref C.TowerPosition);

            ImGuiEx.TextV("平台:");
            ImGui.SameLine();
            ImGuiEx.RadioButtonBool("西", "东", ref C.IsGroup1, true);

            ImGui.Text("近远位引诱:");
            ImGuiEx.RadioButtonBool("基于承技塔", "基于职责", ref C.TakenCheckConditionIsTakenTower, true);
            ImGui.Indent();
            ImGuiEx.TextV($"{"风塔(远位debuff)由谁引诱:"}");
            ImGui.SameLine();
            if (C.TakenCheckConditionIsTakenTower)
            {
                ImGuiEx.RadioButtonBool("土塔玩家", "火塔玩家", ref C.TakenFarIsEarth, true);
            }
            else
            {
                ImGuiEx.RadioButtonBool("土火近战", "土火远程", ref C.TakenFarIsMelee, true);
            }
            ImGui.Unindent();
            ImGui.Unindent();
            

            ImGuiEx.Text(EColor.YellowBright, "回放分摊");
            ImGui.Indent();
            ImGuiEx.RadioButtonBool("固定位置(推荐)", "基于方向", ref C.AltCloneResolution, true);
            if(C.AltCloneResolution)
            {
                ImGuiEx.TextWrapped($"根据团队计划选择两个位置，一个必须在十字方位，另一个在斜方位");
                if(C.AltCloneDirections.Count != 2)
                {
                    ImGuiEx.Text(EColor.RedBright, "配置无效，必须包含两个元素");
                }
                else if(C.AltCloneDirections.Count(x => (int)x % 2 == 1) != 1)
                {
                    ImGuiEx.Text(EColor.RedBright, "配置无效，必须包含一个斜方位方向");
                }
                else if(C.AltCloneDirections.Count(x => (int)x % 2 == 0) != 1)
                {
                    ImGuiEx.Text(EColor.RedBright, "配置无效，必须包含一个十字方位方向");
                }
                else
                {
                    ImGuiEx.Text(EColor.GreenBright, "配置有效");
                }
                if(ImGuiEx.BeginDefaultTable("FixedPositions", ["0", "1", "2", "3", "4", "5", "6"], false))
                {
                    void choice(Direction d)
                    {
                        ImGuiEx.CollectionCheckbox($"##{d}", d, C.AltCloneDirections);
                        ImGuiEx.Tooltip(d.ToString());
                    }
                    ImGui.TableNextRow();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    ImGuiEx.Text("  N");

                    ImGui.TableNextRow();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    choice(Direction.N);

                    ImGui.TableNextRow();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    choice(Direction.NW);
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    choice(Direction.NE);

                    ImGui.TableNextRow();
                    ImGui.TableNextColumn();
                    ImGuiEx.TextV("W");
                    ImGui.TableNextColumn();
                    choice(Direction.W);
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    choice(Direction.E);
                    ImGui.TableNextColumn();
                    ImGuiEx.TextV("E");

                    ImGui.TableNextRow();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    choice(Direction.SW);
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    choice(Direction.SE);

                    ImGui.TableNextRow();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    choice(Direction.S);

                    ImGui.TableNextRow();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    ImGui.TableNextColumn();
                    ImGuiEx.Text("  S");

                    ImGui.EndTable();
                }
            }
            else
            {
                ImGuiEx.TextV($"当分摊分身水平排列(从西到东)时:");
                ImGui.Indent();
                ImGuiEx.RadioButtonBool("取西侧分摊", "取东侧分摊", ref C.StackEnumHorizontalWest, false);
                ImGui.Unindent();
                ImGuiEx.TextV($"当分摊分身垂直排列(从北到南)时:");
                ImGui.Indent();
                ImGuiEx.RadioButtonBool("取北侧分摊", "取南侧分摊", ref C.StackEnumVerticalNorth, false);
                ImGui.Unindent();
                ImGuiEx.TextV($"当分摊分身既不水平也不垂直排列时:");
                ImGui.Indent();
                ImGuiEx.RadioButtonBool("使用水平枚举(从西到东)", "使用垂直枚举(从北到南)", ref C.StackEnumPrioHorizontal, false);
                ImGui.Unindent();
            }
            ImGui.Unindent();
        }

        if(ImGui.CollapsingHeader("调试"))
        {
            ImGuiEx.Text($"可见分身数: {this.AllClones.Count()}");
            if(ImGui.Button("复制状态"))
            {
                GenericHelpers.Copy(JsonConvert.SerializeObject(State));
            }
            if(ImGui.Button("粘贴状态"))
            {
                try
                {
                    var state = JsonConvert.DeserializeObject<StateDef>(GenericHelpers.Paste()!) ?? throw new NullReferenceException();
                    State = state;
                }
                catch(Exception e)
                {
                    e.Log();
                }
            }
            ImGui.InputInt("阶段", ref State.Phase);
            ImGui.InputInt("诽谤次数", ref State.DefamationAttack);
            ImGui.InputInt("11阶段子状态", ref State.Phase11Sub);
            ImGuiEx.Text($"调整后诽谤次数 {GetAdjustedDefamationNumber()}");
            ImGuiEx.Checkbox("下次扇形南北", ref this.State.NextCleavesNorthSouth);
            ImGuiEx.Checkbox("是否先十字", ref this.State.IsCardinalFirst);
            ImGuiEx.Checkbox("是否递减抗性", ref State.IsThDecreasingResistance);
            ImGuiEx.Checkbox("扇形安全区是否在北", ref State.IsConeSafeNorth);
            ImGui.Separator();
            ImGuiEx.Text($"下次扇形:\n{State.NextCleavesList.Select(x => $"{x.Pos} {x.Rot.RadToDeg()}").Print("\n")}");
            ImGui.Separator();
            ImGuiEx.Text($"下次AOE: {State.NextAOE}");
            ImGui.Separator();
            ImGuiEx.Text($"下次扇形南北: {State.NextCleavesNorthSouth}");
            ImGuiEx.Text($"顺序:\n{this.State.PlayerOrder.Select(x => $"{x.Key.GetObject()}: {x.Value}").Print("\n")}");
            ImGuiEx.Text($"诽谤:\n{this.State.DefamationPlayers.Select(x => $"{x.Key.GetObject()}: {x.Value}").Print("\n")}");
            ImGuiEx.Text($"分身位置:\n{this.State.ClonePositions.Select(x => $"{x.Key.GetObject()}: {x.Value}").Print("\n")}");
            ImGui.Separator();
            ImGuiEx.Text($"获取Boss分身:\n{GetBossClones().Print("\n")}");
            ImGui.Separator();
            ImGui.Text("Towers Debug:");
            if (State.TowersDebug != null)
            {
                foreach (var (name, obj) in State.TowersDebug.Where(x => x.Item2.GetObject() != null))
                    ImGuiEx.Text($"{name}: NPCID: {obj.GetObject().Struct()->GetNameId()} Pos: {obj.GetObject()?.Position}");
            }
            
            ImGui.Separator();
            ImGuiEx.Text("TowerDataArray:");
            foreach (var t in State.TowerDataArray)
            {
                ImGuiEx.Text(t.AssignToPlayerEntityId.TryGetPlayer(out var p)
                    ? $"{t.kinds} ({t.Side}): Pos={t.Position}, AssignedTo={p.GetNameWithWorld()}"
                    : $"{t.kinds} ({t.Side}): Pos={t.Position}, AssignedTo={t.AssignToPlayerEntityId}");
            }

            if (Svc.Condition[ConditionFlag.DutyRecorderPlayback])
            {
                ImGui.Separator();
                ImGuiEx.Text("DebugOverWritesTower:");
                if (ImGui.BeginCombo("OverWritesTower##overwrite", State.DebugOverWriteTower.ToString()))
                {
                    if (ImGui.Selectable("无", State.DebugOverWriteTower == null)) State.DebugOverWriteTower = null;

                    foreach (Towers t in Enum.GetValues(typeof(Towers)))
                        if (ImGui.Selectable(t.ToString(), State.DebugOverWriteTower == (Towers)t))
                            State.DebugOverWriteTower = (Towers)t;

                    ImGui.EndCombo();
                }
            }

            ImGui.Separator();
            ImGui.Text("元素:");
            foreach (var e in Controller.GetRegisteredElements())
                ImGuiEx.Text(
                    $"{e.Key}: Enabled={e.Value.Enabled} Pos=({e.Value.refX}, {e.Value.refZ}, {e.Value.refY})");
        }
    }

    public enum TowerPosition { MeleeLeft, MeleeRight, RangedLeft, RangedRight }
    public enum PickupOrder { Defamation_1, Defamation_2, Defamation_3, Defamation_4, Stack_1, Stack_2, Stack_3, Stack_4 }

    public class Config : IEzConfig
    {
        public TowerPosition TowerPosition = default;
        public bool IsGroup1 = true;
        public bool TakenCheckConditionIsTakenTower = false;
        public bool TakenFarIsEarth = true;
        public bool TakenFarIsMelee = true;
        public List<PickupOrder> Pickups = [PickupOrder.Stack_1, PickupOrder.Stack_2, PickupOrder.Stack_3, PickupOrder.Stack_4, PickupOrder.Defamation_1, PickupOrder.Defamation_2, PickupOrder.Defamation_3, PickupOrder.Defamation_4];
        public bool StackEnumPrioHorizontal = false;
        public bool StackEnumVerticalNorth = true;
        public bool StackEnumHorizontalWest = true;
        public bool NoRainbow = false;
        public Vector4 FixedColor = EColor.RedBright;
        public bool AltCloneResolution = false;
        public List<Direction> AltCloneDirections = [];
        public bool ShowTetherLine = true;
        public bool ShowTetherCircle = true;
        public HashSet<Direction> LP2CardinalStackFirst = [Direction.N, Direction.NE, Direction.E, Direction.SE];
        public HashSet<Direction> LP2CardinalDefamationFirst = [Direction.N, Direction.NE, Direction.E, Direction.SE];
        public bool SkipIndiMechs = false;
        public string Comment = "";

        // preliminary
        public bool DontShowElementsP11S1 = false;
    }
    Config C => Controller.GetConfig<Config>();

    public Vector4 GetRainbowColor(double cycleSeconds)
    {
        if(C.NoRainbow) return C.FixedColor;
        if(cycleSeconds <= 0d)
        {
            cycleSeconds = 1d;
        }

        var ms = Environment.TickCount64;
        var t = (ms / 1000d) / cycleSeconds;
        var hue = t % 1f;
        return HsvToVector4(hue, 1f, 1f);
    }

    public static Vector4 HsvToVector4(double h, double s, double v)
    {
        double r = 0f, g = 0f, b = 0f;
        var i = (int)(h * 6f);
        var f = h * 6f - i;
        var p = v * (1f - s);
        var q = v * (1f - f * s);
        var t = v * (1f - (1f - f) * s);

        switch(i % 6)
        {
            case 0: r = v; g = t; b = p; break;
            case 1: r = q; g = v; b = p; break;
            case 2: r = p; g = v; b = t; break;
            case 3: r = p; g = q; b = v; break;
            case 4: r = t; g = p; b = v; break;
            case 5: r = v; g = p; b = q; break;
        }

        return new Vector4((float)r, (float)g, (float)b, 1f);
    }
}
