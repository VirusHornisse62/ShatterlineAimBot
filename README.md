# ShatterlineAimBot
Shatterline Color Aimbot

DOWNLOAD HERE https://github.com/VirusHornisse62/ShatterlineAimBot/releases/tag/Compiled , or...

just copy this setting in nvidia inspector 2.3.0.10 and then use any color aimbot.

Antialiasing - transparency supersampling: AA_MODE_REPLAY_MODE_ALL
Texture filtering - Driver Controlled LOD Bias: Off
Texture filtering - LOD Bias (DX): 0x00000078
Texture filtering - LOD Bias (OGL): 0x00000078
Texture filtering - Quality: High performance

https://github.com/Orbmu2k/nvidiaProfileInspector/releases/tag/2.3.0.10

Compile Code in Visual Studio:

```using System;
using System.Collections;
using System.Collections.Generic;
using System.Drawing;
using System.Drawing.Imaging;
using System.Linq;
using System.Numerics;
using System.Runtime.InteropServices;
using System.Threading.Tasks;
 
namespace ShatterlineColorAimbot
{
    class Program
    {
        //SIZE
        const int xSize = 1920; //res
        const int ySize = 1080; //res
 
        //FOV in pixels, smaller fov will result in faster update time
        const int maxX = 90; //fov
        const int maxY = 90; //fov
 
        // GAME
        const float speed = 1.35f; //0.35 slow
        const int msBetweenShots = 0;
        const int closeSize = 10;
        const bool canShoot = false;
 
        // COLOR
        const int color = 0xc60912;    //0xb41515 = Red;
        const int colorVariation = 20;
 
        const double size = 60;  // DONT CHANGE
        const int maxCount = 5;
 
        static void Main(string[] args)
        {
            Update();
        }
 
        static void Update()
        {
            System.DateTime lastshot = System.DateTime.Now;
 
            while (true)
            {
                Task.Delay(1); // ANTI CRASH
                if (GetAsyncKeyState(0x02) == 0) //0x01 = LBUTTON ::: 0x02 = RBUTTON ::: 0x05 = X1Button ::: 0x06 = X2Button ::: 0x06 = X2Button ::: 0xA0 = LSHIFT ::: 0xA4 = LALT
                    continue;
                var l = PixelSearch(new Rectangle((xSize - maxX) / 2, (ySize - maxY) / 2, maxX, maxY), Color.FromArgb(color), colorVariation);
                if (l.Length > 0)
                { // IF NOT ERROR
                    var q = l.OrderBy(t => t.Y).ToArray();
 
                    List<Vector2> forbidden = new List<Vector2>();
 
                    for (int i = 0; i < q.Length; i++)
                    {
                        Vector2 current = new Vector2(q[i].X, q[i].Y);
                        if (forbidden.Where(t => (t - current).Length() < size || Math.Abs(t.X - current.X) < size).Count() < 1)
                        { // TO NOT PLACE POINTS AT THE BODY
                            forbidden.Add(current);
                            if (forbidden.Count > maxCount)
                            {
                                break;
                            }
                        }
                    }
 
                    // DRAW
                    /*foreach (var c in forbidden) {
                        DrawRec((int)c.X, (int)c.Y - 20, 5, 5);
                    }*/
 
                    // SHOOTING
                    bool pressDown = false;
                    var closes = forbidden.Select(t => (t - new Vector2(xSize / 2, ySize / 2))).OrderBy(t => t.Length()).ElementAt(0) + new Vector2(1, 1);
                    if (closes.Length() < closeSize)
                    {
                        if (canShoot)
                        {
                        }
                    }
 
                    Move((int)(closes.X * speed), (int)(closes.Y * speed), pressDown);
                }
            }
        }
 
        [DllImport("user32.dll")]
        static extern void mouse_event(int dwFlags, int dx, int dy, uint dwData,
UIntPtr dwExtraInfo);
 
        [DllImport("user32.dll")]
        static extern short GetAsyncKeyState(int vKey);
 
        public static void Move(int xDelta, int yDelta, bool pressDown = false)
        {
            mouse_event(pressDown ? (MOUSEEVENTF_LEFTDOWN | MOUSEEVENTF_LEFTUP) : 0x0001, xDelta, yDelta, 0, UIntPtr.Zero);
        }
 
        private const int MOUSEEVENTF_LEFTDOWN = 0x02;
        private const int MOUSEEVENTF_LEFTUP = 0x04;
        private const int MOUSEEVENTF_RIGHTDOWN = 0x08;
        private const int MOUSEEVENTF_RIGHTUP = 0x10;
 
        public static Point[] PixelSearch(Rectangle rect, Color Pixel_Color, int Shade_Variation) // REZ is for debugging
        {
            ArrayList points = new ArrayList();
            Bitmap RegionIn_Bitmap = new Bitmap(rect.Width, rect.Height, PixelFormat.Format24bppRgb);
            using (Graphics GFX = Graphics.FromImage(RegionIn_Bitmap))
            {
                GFX.CopyFromScreen(rect.X, rect.Y, 0, 0, rect.Size, CopyPixelOperation.SourceCopy);
            }
            BitmapData RegionIn_BitmapData = RegionIn_Bitmap.LockBits(new Rectangle(0, 0, RegionIn_Bitmap.Width, RegionIn_Bitmap.Height), ImageLockMode.ReadWrite, PixelFormat.Format24bppRgb);
            int[] Formatted_Color = new int[3] { Pixel_Color.B, Pixel_Color.G, Pixel_Color.R }; //bgr
 
            unsafe
            {
                for (int y = 0; y < RegionIn_BitmapData.Height; y++)
                {
                    byte* row = (byte*)RegionIn_BitmapData.Scan0 + (y * RegionIn_BitmapData.Stride);
                    for (int x = 0; x < RegionIn_BitmapData.Width; x++)
                    {
                        if (row[x * 3] >= (Formatted_Color[0] - Shade_Variation) & row[x * 3] <= (Formatted_Color[0] + Shade_Variation)) //blue
                            if (row[(x * 3) + 1] >= (Formatted_Color[1] - Shade_Variation) & row[(x * 3) + 1] <= (Formatted_Color[1] + Shade_Variation)) //green
                                if (row[(x * 3) + 2] >= (Formatted_Color[2] - Shade_Variation) & row[(x * 3) + 2] <= (Formatted_Color[2] + Shade_Variation)) //red
                                    points.Add(new Point(x + rect.X, y + rect.Y));
                    }
                }
            }
            RegionIn_Bitmap.Dispose();
            return (Point[])points.ToArray(typeof(Point));
        }
    }
}```
