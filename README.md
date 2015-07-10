package temochka;

abstract class Function2 {
	public abstract double func2(double x);
	public abstract double func2_(double x);
	public abstract double func2__(double x);
	
	public double[]middle_point(double a, double b, double eps)
	{
		double minX=Double.NaN,minY=Double.NaN, iter=0, diter = 1, d2iter = 0;
		double leftX = a, rightX = b;
		double newX,newY;
		while(true)
		{
			newX = (leftX + rightX) / 2;
			newY = func2_(newX);
			diter++;
			iter++;
			if(Math.abs(newX - leftX) <= eps || Math.abs(newX - rightX) <= eps) 
			{
				minX = newX;
				minY = func2(minX);
				break;
			}
			else 
			{
				if(newY > 0) rightX = newX;
				else if (newY < 0) leftX = newX;
			}
		}
		return new double[]{minX,minY,iter};
	}
	
	public double[]hord(double a, double b, double eps)
	{
		double minX=Double.NaN, minY=Double.NaN, iter=1, diter = 0, d2iter;
		double leftX = a, rightX = b;
		double f_leftX,f_rightX;
		double xabs;
		
		f_leftX = func2_(leftX);
		f_rightX = func2_(rightX);
		diter+=2;
		iter +=2;
		if((f_leftX * f_rightX) > 0) System.out.println("No solution!");
		else
		{
			while(true)
			{
				xabs = leftX - (f_leftX/(f_leftX - f_rightX)) * (leftX - rightX);
				if(Math.abs(xabs - leftX) <= eps || Math.abs(xabs - rightX) <= eps) 
				{
					minX = xabs;
					minY = func2(xabs);
					iter++;
					break;
				}
				
					if(func2_(xabs) > 0)
					{
						rightX = xabs;
						f_rightX = func2_(xabs);
						diter++;
						iter++;
					}
					else if(func2_(xabs) < 0)
					{
						leftX = xabs;
						f_leftX = func2_(xabs);
						diter++;
						iter++;
					}
			}
		}
		return new double[]{minX,minY,iter};
	}
	
	public double[]newtons(double a, double b, double eps, double Xeps){
		double minX=Double.NaN,minY=Double.NaN,iter=0, diter = 0, d2iter = 0;
		double Xn = Double.NaN,Xnn = Xeps;
		
		while(true)
		{
			Xn = Xnn - func2_(Xnn)/func2__(Xnn);
/*			FirstFrame.println(Xn,func2(Xnn),func2_(Xnn));
			try {
				Thread.sleep(500);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			*/
			diter++;
			d2iter++;
			iter+=2;
			if(Math.abs(Xn-Xnn) <= eps)
			{
				minX = Xn;
				minY = func2(Xn);
				iter++;
				break;
			}
			Xnn = Xn;
		}
		return new double[]{minX,minY,iter};
	}

	public double[]newtons_rafson(double a, double b, double eps, double Xeps){
		double minX=Double.NaN,minY=Double.NaN,iter=0, diter = 0, d2iter = 0;
		double Xn = Double.NaN,Xnn = Xeps, Tk, x_;
		
		x_ = Xnn - func2_(Xnn)/func2__(Xnn);
		Tk = (Math.pow(func2_(Xnn),2)/Math.pow((Math.pow(func2__(Xnn),2) + func2_(x_)),2));
		iter += 5;
		diter += 3;
		d2iter += 2;
		
		while(true)
		{
			Xn = Xnn - Tk*(func2_(Xnn)/func2__(Xnn));
			iter += 2;
			diter++;
			d2iter++;
			if(Math.abs(Xn-Xnn) <= eps)
			{
				minX = Xn;
				minY = func2(Xn);
				iter++;
				break;
			}
			Xnn = Xn;
		}
		return new double[]{minX,minY,iter};
	}
	
	public double[]newtons_mark(double a, double b, double eps, double Xeps){
		double minX=Double.NaN,minY=Double.NaN,iter=0, diter = 0, d2iter = 0;
		double Xn = Double.NaN,Xnn = Xeps, u;
		
		
		while(true)
		{
			u = func2__(Xnn) * eps;
			Xn = Xnn - (func2_(Xnn)/(func2__(Xnn) + u));
			iter += 3;
			d2iter += 2;
			diter++;
			if(Math.abs(Xn-Xnn) <= eps)
			{
				minX = Xn;
				minY = func2(Xn);
				iter++;
				break;
			}
			if(func2(Xn) < func2(Xnn)) 
				{
					u /= 2;
					iter += 2;
				}
			else u *= 2;
			Xnn = Xn;
		}
		return new double[]{minX,minY,iter};
	}
	
	public double[]perebor(double a, double b, double eps)
	{
		double minX=a,minY=func2(a),iter = 0, diter = 0, d2iter = 0;
		double x0 = a , x1 = a;
		double newX = a;
		double leftX=a;
		double promL1 = func2_(a);

		double L;
		for(double j=a+eps; j <= b; j+=eps)
		{
			L = func2_(j);
			if(L > promL1) 
				{
					promL1 = L;	
					leftX = j;
				}
		}	
		iter++;
		
		while(true)
		{
			x1 = x0;
		
			if(func2(x1)<minY)
			{
				minX = x1;
				minY = func2(x1);
				iter++;
			}
			if(x1>=leftX)break;
			x0+=eps;
		}
		return new double[]{minX,minY,iter};
	}
	
	public double[]breaking(double a, double b, double eps)
	{
		double minX=Double.NaN,minY=Double.NaN,iter = 0, diter = 0, d2iter = 0;
		double Yn = func2(a), Xn = a, lastY = func2(a);
		int n = (int)(Math.abs(a - b) * eps);
		iter += 2;
		while(true)
		{
			Yn = lastY + eps * func2_(Xn);
			Xn += eps;
			iter++;
			diter++;
			if(lastY - Yn < 0)
			{
				minX = Xn;
				minY = func2(Xn);
				iter++;
				break;
			}
			lastY = Yn;
		}
		return new double[]{minX,minY,iter};
	}
	
	public double[]breaking_line(double a, double b, double eps)
	{
		double minX=Double.NaN,minY=Double.NaN,iter = 0, diter = 0, d2iter = 0;
		double Xn, promL1 = Double.NaN, promL2 = func2_(a);
		
		int n = ((int)(Math.abs(a - b) / (eps)));
		double x0, y0;
		double L;
		int min_idx = 0;
		
		double leftA = a;
		double leftB = b;

		double[]lom_x = new double[n];
		double[]lom_y = new double[n];
		double fx, px, delta, min_x1, min_x2, min_y;
		
		for(double j=a+eps; j <= b; j+=eps)
		{
			L = func2_(j);
			if(L > promL2) 
				{
					promL1 = L;
					Xn = j;
				}
			promL2 = L;
		}
//		promL1 = 0.00001;
		promL1 = 13;
		x0 = (func2(leftA) - func2(leftB) + promL1 * (leftA + leftB)) / (2 * promL1);
		y0 = (func2(leftA) + func2(leftB) + promL1 * (leftA - leftB)) / (2);
		lom_x[0] = x0;
		lom_y[0] = y0;
		for(int i=1; i<n; i++)
		{
			leftA += eps;
			leftB = leftA + eps;
			x0 = (func2(leftA) - func2(leftB) + promL1 * (leftA + leftB)) / (2 * promL1);
			y0 = (func2(leftA) + func2(leftB) + promL1 * (leftA - leftB)) / (2);
			lom_x[i] = x0;
			lom_y[i] = y0;
		}
		
		while(true)
		{
			min_idx = 0;
			
			for(int i = 1; i<n; i++)
				if (lom_y[i] < lom_y[min_idx])  min_idx = i;
			
			fx = func2(lom_x[min_idx]); iter++;
			px = lom_y[min_idx];
			delta = (fx - px) / (2 * promL1);
			
			if((2 * promL1 * delta) <= eps)
			{
				minX = lom_x[min_idx];
				minY = fx;
				break;
			}
			min_x1 = lom_x[min_idx] - delta;
			min_x2 = lom_x[min_idx] + delta;
			min_y = (fx + px) / 2;
			lom_x[min_idx] = min_x1;
			lom_y[min_idx] = min_y;
			
			lom_x[0] = min_x2;
			lom_y[0] = min_y;
		}
		
		return new double[]{minX,minY,iter};
	}
	
	public double[]breaking_line2(double a, double b, double eps)
	{
		double minX=Double.NaN,minY=Double.NaN,iter = 0, diter = 0, d2iter = 0;
		double promL1 = Double.NaN, promL2 = func2_(a);
		double leftA = a, leftB = b; 
		
		int n = ((int)(Math.abs(a - b) / (eps)));
		double x0, y0, x1, x2, y1, y2;
		double L;
		
		for(double j=a+eps; j <= b; j+=eps)
		{
			L = func2_(j);
			if(L > promL2) 
				{
					promL1 = L;
				}
			promL2 = L;
		}
//		promL1 = 0.5;
//		promL1 = 20;
		
		while(true)
		{
			x0 = (func2(leftA) - func2(leftB) + promL1 * (leftA + leftB)) / (2 * promL1);
			y0 = (func2(leftA) + func2(leftB) + promL1 * (leftA - leftB)) / (2);
			x1 = (func2(leftA) - func2(x0) + promL1 * (leftA + x0)) / (2 * promL1);
			y1 = (func2(leftA) + func2(x0) + promL1 * (leftA - x0)) / 2;
			x2 = (func2(x0) - func2(leftB) + promL1 * (x0 + leftB)) / (2 * promL1);
			y2 = (func2(x0) + func2(leftB) + promL1 * (x0 - leftB)) / 2;
			
			iter += 3;
			
			if(func2(x1) > func2(x0)) 
				{
					leftA = x0;
				}
			else if (func2(x0) < func2(x2))
				{
					leftB = x0;
				}
			else leftA = x0;
			
			if((Math.abs(x0 - x1) < eps) || (Math.abs(x0 - x2) < eps))
			{
				minX = x0;
				minY = func2(x0);
				iter += 1;
				break;
			}
		}
		return new double[]{minX,minY,iter};
	}
}

