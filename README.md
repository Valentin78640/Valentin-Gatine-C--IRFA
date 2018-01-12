#include <iostream>
#include <cmath>
#include <iomanip>

using namespace std;

double PV(double futureValue, double couponRate, double r, int N,double d)
{
    double temp_PV=0;
    double coupon=couponRate*futureValue;
    // The loop "for" is usefull to group the initialisation, the condition and the incrementaiton process
    for(int i=0;i<=N;i++)
    {
        temp_PV += coupon*pow(1+r,-i-d);
    }

    temp_PV += + futureValue*pow(1+r,-N-d);

    return temp_PV;
}

// Compute the Yield-to-Maturity(YTM) by iteration : Secant method
double iterationYTM(double futureValue,double couponRate,double priceValue,int N,double d,double i0=0.00001, double i1= 10)
{
    //number of iteration
    const int MaxIter=50000;
    //error limit
    double epsilon = 0.0000001;
    //temporary iterative values
    double midP=0.0,midCdif;
    //Algorithm interval
    double lowCdif=priceValue- PV(futureValue, couponRate, i0, N,d);
    double highCdif=priceValue- PV(futureValue, couponRate, i1, N,d);

    if(priceValue == futureValue)
    {
        return couponRate;
    }

    else if(couponRate == 0)
    {
        return  pow((futureValue/priceValue),1/N+d)-1;
    }
    if (lowCdif*highCdif>0)
        return -1;
    else
        for(int i=0;i<=MaxIter;i++){
            midP=(i0+i1)/2.0;
            midCdif=priceValue- PV(futureValue, couponRate, midP, N,d);
            if(abs(midCdif)<epsilon) return midP;
            else
            {
                if (midCdif>0) i1=midP;
                else i0=midP;
            }

        }

    return midP;
}


int main()
{

    double couponRate, d, futureValue, priceValue,coupon,YTM_percentage;
    int N;

    cout << "Coupon value" << endl;
    cin >> coupon;

    cout << "Year fraction until next coupon payment" << endl;
    cin >> d;
    while(d>1 || d<0)
    {
        cout << "be careful : Please enter a value between 0 and 1 for d !" << endl;
        cin >> d;
    }

    cout << "Future Value" << endl;
    cin >> futureValue;
    while(futureValue < 0)
    {
        cout << "be careful : Please enter a positive value for the Future Value !" << endl;
        cin >> futureValue;
    }

    cout << "Present Value" << endl;
    cin >> priceValue;
    while(priceValue < 0)
    {
        cout << "be careful : Please enter a positive value for the Present Value !" << endl;
        cin >> priceValue;
    }

    cout << "Number of full years until maturity" << endl;
    cin >> N;   // I did multiple cout because if find it cleaner when you enter the data in the console
    while(N<0)
    {
        cout << "be careful : Please enter a positive and whole number for N !" << endl;
        cin >> N; // I know there is repetitions but it is just some restrictions. I could also have but these restrictions directly in the function.
    }

    couponRate=coupon/futureValue;

    double YTM = iterationYTM(futureValue,couponRate,priceValue,N,d);
    YTM_percentage = YTM*100;
    cout << "Yield to maturity = " << setprecision(3) << YTM_percentage << "%" << endl;

return 0;
}
