#include <stdio.h>
#include <stdlib.h>

// Variable used to generate pseudo-random numbers
unsigned int seed;

// Function to generate pseudo-random numbers
inline int myRandom() {
  seed = (214013*seed+2531011);
  return (seed>>13);
}

void InitKernel( int *k, unsigned Klen )
{
  for ( int i=0; i< Klen; i++ ) {
    k[i] = myRandom() & 7;
  }
}

struct pixel { unsigned char * __restrict x; unsigned char * __restrict y; unsigned char * z; };

void InitImage( struct pixel I, unsigned N )
{
  for ( int j=0; j< N; j++ ) 
  {
    I.x[j] = myRandom() % 31;
    I.y[j] = myRandom() % 31;
    I.z[j] = myRandom() % 31;
  }
}

void TransfImage( struct pixel I, unsigned N, unsigned Klen, unsigned char * __restrict Kx, unsigned char* __restrict Ky, unsigned char* __restrict Kz )
{
  struct pixel T;
  
  T.x = malloc( N*sizeof(unsigned char) );
  T.y = malloc( N*sizeof(unsigned char) );
  T.z = malloc( N*sizeof(unsigned char) );

  // copy I to T in order to prevent data dependencies
  for ( int i=0; i< N; i++ ){
    T.x[i] = I.x[i];
    T.y[i] = I.y[i];
    T.z[i] = I.z[i];
  }

  for ( int i=0; i< N; i++ ) 
  {
    unsigned vx=0, vy=0, vz=0, sum;
    unsigned newKlen = (N-i);
    newKlen = (newKlen < Klen)? newKlen : Klen;
    
    for ( int k=0; k < newKlen; k++ )
    {
      int image_i = i + k;
    
      vx += T.x[ image_i ]&Kx[k];
     
      vy += T.y[ image_i ]&Ky[k];
   
      vz += T.z[ image_i ]&Kz[k];
    }

    I.x[i] = vx; 
    I.y[i] = vy; 
    I.z[i] = vz; 
    sum   = I.x[i]+I.y[i]+I.z[i]+1;
    I.x[i] = I.x[i]*31 / sum;
    I.y[i] = I.y[i]*31 / sum;
    I.z[i] = I.z[i]*31 / sum;
  }

  free(T.x);
  free(T.y);
  free(T.z);
}

int main (int argc, char **argv)
{
  int i, sumX, sumY, sumZ, N=10000, Klen=2000, Rep=1000;

  seed = 12345;

  // obtain parameters at run time
  if (argc>1) { N    = atoi(argv[1]); }
  if (argc>2) { Klen = atoi(argv[2]); }
  if (argc>3) { Rep  = atoi(argv[3]); }

  printf("Challenge #1: Vector size is %d. Kernel size is %d. Repeat %d times\n", N, Klen, Rep);

  // Create Image & Kernel
  struct pixel image;
  image.x = malloc( N*sizeof(unsigned char) );
  image.y = malloc( N*sizeof(unsigned char) );
  image.z = malloc( N*sizeof(unsigned char) );
  
  int *K = (int *) malloc( Klen*sizeof(int) );
 
  InitImage ( image, N );
  InitKernel( K, Klen );
  
  unsigned char *Kx = (unsigned char*)malloc(Klen*sizeof(unsigned char));
  unsigned char *Ky = (unsigned char*)malloc(Klen*sizeof(unsigned char));
  unsigned char *Kz = (unsigned char*)malloc(Klen*sizeof(unsigned char));
	
  for (i = 0; i<Klen; i++)
  {
    Kx[i] = 0;
    Ky[i] = 0;
    Kz[i] = 0;

    if(((K[i] >> 2) & 1) == 1)
	    Kx[i] = 31;
    if(((K[i] >> 1) & 1) == 1)
    	Ky[i] = 31;
    if((K[i] & 1) == 1)
	    Kz[i] = 31;
  }

  // Repeat
  for (i=0; i<Rep; i++)
  {
    TransfImage( image, N, Klen, Kx, Ky, Kz); 
    int ii;
    ii = myRandom() % N;  image.x[ii] = myRandom() % 31;
    ii = myRandom() % N;  image.y[ii] = myRandom() % 31;
    ii = myRandom() % N;  image.z[ii] = myRandom() % 31;
  }

  for (i=0, sumX=0; i<N; i++) sumX += image.x[i];
  for (i=0, sumY=0; i<N; i++) sumY += image.y[i];
  for (i=0, sumZ=0; i<N; i++) sumZ += image.z[i];
    
  printf("Result: sumX= %d, sumY= %d, sumZ= %d\n", sumX, sumY, sumZ);

  free(image.x); free(image.y); free(image.z); free(K);

  return 0;
}
