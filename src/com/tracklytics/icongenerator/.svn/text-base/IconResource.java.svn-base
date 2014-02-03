package com.tracklytics.icongenerator;

import java.awt.Color;
import java.awt.Font;
import java.awt.Graphics2D;
import java.awt.geom.AffineTransform;
import java.awt.image.AffineTransformOp;
import java.awt.image.BufferedImage;
import java.awt.image.LookupOp;
import java.awt.image.ShortLookupTable;
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.File;
import java.io.InputStream;

import javax.imageio.ImageIO;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.QueryParam;
import javax.ws.rs.core.Response;

//http://localhost:8080/icongenerator/services/icons?color=0,0,0&direction=135&label=121&size=large

@Path("icons")
public class IconResource {
	
	private static String mStartArrowDir = "/mnt/resources/icons";
	
	
	private static BufferedImage scaleImage(BufferedImage img, String size) {
		if (size.equals("small")) {
			return img;
		}
		else if (size.equals("medium")) {
			BufferedImage newimg = new BufferedImage(60, 60,BufferedImage.TYPE_INT_ARGB);
		    AffineTransform tx = new AffineTransform();
		    tx.scale(1.5, 1.5);
		    AffineTransformOp op = new AffineTransformOp(tx,AffineTransformOp.TYPE_BILINEAR);
		    newimg=op.filter(img, null);
		    return newimg;
		}
		else if (size.equals("large")) {
			BufferedImage newimg = new BufferedImage(80, 80,BufferedImage.TYPE_INT_ARGB);
		    AffineTransform tx = new AffineTransform();
		    tx.scale(2.0, 2.0);
		    AffineTransformOp op = new AffineTransformOp(tx,AffineTransformOp.TYPE_BILINEAR);
		    newimg=op.filter(img, null);
		    return newimg;
		}
		else {
			BufferedImage newimg = new BufferedImage(160, 160,BufferedImage.TYPE_INT_ARGB);
		    AffineTransform tx = new AffineTransform();
		    tx.scale(4.0, 4.0);
		    AffineTransformOp op = new AffineTransformOp(tx,AffineTransformOp.TYPE_BILINEAR);
		    newimg=op.filter(img, null);
		    return newimg;
		}
	
	}
	
	private static BufferedImage rotateImage(BufferedImage img, double rotate) {
	    int w = img.getWidth();
	    int h = img.getHeight();
		    BufferedImage rot = new BufferedImage(h, w, BufferedImage.TYPE_INT_ARGB);
		    //double theta = Math.PI / 2;
		    AffineTransform xform = new AffineTransform();
		    xform.rotate(rotate * Math.PI / 180.0, img.getWidth() / 2.0, img.getHeight() / 2.0);
		    Graphics2D g = (Graphics2D) rot.createGraphics();
		    g.drawImage(img, xform, null);
		    g.dispose();
		    return rot;
	}
	private static BufferedImage colorImage(BufferedImage img, String colors) {
		
		try {
		    short[] r = new short[256];
		    short[] g = new short[256];
		    short[] b = new short[256];
		    short[] a = new short[256];
		    
		    for (short i = 0; i < 256; i++) {
		        r[i] = i;
		        g[i] = i; 
		        b[i] = i;
		        a[i] = i;
		    }
		    String[] a1 = colors.split(",");
		    
			r[0] = Short.parseShort(a1[0]); 
			g[0] = Short.parseShort(a1[1]); 
			b[0] = Short.parseShort(a1[2]);
			short[][] colorFilter = new short[][]{r, g, b, a};
			LookupOp  colorFilterOp = new LookupOp(new ShortLookupTable(0, colorFilter), null);
			BufferedImage newimg;
			newimg = colorFilterOp.filter(img, img);
			return newimg;
		}
		catch (Exception e) {
			System.out.println(e.getMessage());
		}
		return null;
	}
	
	private static BufferedImage labelImage(BufferedImage img, String label, String size) {
	
		int w = 0;
		int h = 0;
		int fsize = 0;
		int start = 0;
		int x = 0;
		int y = 0;
		
		if (size.equals("small")) {
			w=h=64;
	    	fsize = 26;
	    	start = w-40;
	    	x=-1;
	    	y=19;
		}
		else if (size.equals("medium")) {
			w=h=96;
	    	fsize = 42;
	    	start = w-60;
	    	x=-1;
	    	y=34;
		}
		else if (size.equals("large")) {
			w=h=128;
			fsize = 64;
			start = w-80;
	    	x=-1;
	    	y=48;

		}
		else if (size.equals("huge")) {
			w=h=256;
			fsize = 96;
			start = w-160;
	    	x=-1;
	    	y=72;

		}	    
	    BufferedImage newimg = new BufferedImage(w, h, BufferedImage.TYPE_INT_ARGB);
	    Graphics2D g2d = newimg.createGraphics();
	    g2d.drawImage(img, start, start, null);
	    g2d.setPaint(Color.black);
	    g2d.setFont(new Font( "SansSerif", Font.BOLD, fsize ));
	    String s = label;
	    //FontMetrics fm = g2d.getFontMetrics();
	    g2d.drawString(s,x,y);
	    g2d.dispose();
	    return newimg;
		
	}
	
	private BufferedImage doIcon(String color, double heading, String label, String size) {
		try {
			BufferedImage img = ImageIO.read(new File(mStartArrowDir, "000d.png"));
			
			img = scaleImage(img, size);
			img = rotateImage(img, heading);
			img = labelImage(img, label,size);
			img = colorImage(img, color);
			return img;	
		}
		catch (Exception e) {
			System.out.println(e.getMessage());
			e.printStackTrace();			
		}
		return null;
	}
	
	
	@GET
	@Produces("image/png")
	public Response getIcon(@QueryParam("label") String label,
			@QueryParam("color") String color,
			@QueryParam("dir") String direction,
			@QueryParam("size") String size) {
		
		final ByteArrayOutputStream out = new ByteArrayOutputStream();
	
		try {
			
			BufferedImage bi = this.doIcon(color,Double.parseDouble(direction),label,size);
			ImageIO.write(bi, "PNG",out);
			final byte[] imgData = out.toByteArray();
			final InputStream bigInputStream = new ByteArrayInputStream(imgData);
			return Response.ok(bigInputStream).build();
			
		}
		catch (Exception e) {
			System.out.println(e.getMessage());
			e.printStackTrace();
		}
		return null;
	}
}