# message-encrypt-and-decrypt-tool-in-c
#include <math.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int isPrime(int n) {
  int i;
  for (i = 2; i <= sqrt(n); i++) {
    if (n % i == 0) {
      return 0;
    }
  }
  return 1;
}

int gcd(int a, int b) {
  if (a == 0) {
    return b;
  }
  return gcd(b % a, a);
}

int totient(int p, int q) { return (p - 1) * (q - 1); }

int randome(int lambda_n) {
  printf("\nThe number e should be less than %d\n and greater than 1.",
         lambda_n);
  for (int i = 2; i < lambda_n; i++) {
    if (gcd(i, lambda_n) == 1) {
      return i;
    }
  }
  return -1;
}

int private_key(int e, int lambda_n) {
  for (int i = 1; i < lambda_n; i++) {
    if ((i * e) % lambda_n == 1) {
      printf("\nThus, (i * e) %% lambda_n = 1, (%d * %d) %% %d = 1", i, e,
             lambda_n);
      return i;
    }
  }
  return -1;
}

long pomod(long a, long b, long m) {
  long x = 1, y = a;
  while (b > 0) {
    if (b % 2 == 1) {
      x = (x * y) % m;
    }
    y = (y * y) % m;
    b /= 2;
  }
  return x % m;
}

/* Encryption
 * A function which takes the message, the public key and a number n which is he
 * product of p and q. The function encrypts the message using the public key
 * and returns the encrypted message.
 */

char *encrypt(char *message, long e, long n) {
  long i;
  long len = strlen(message);
  char *cipher = (char *)malloc(len * sizeof(char));
  for (i = 0; i < len; i++) {
    cipher[i] = pomod(message[i], e, n);
    printf("\n%c -> %c", message[i], cipher[i]);
  }
  return cipher;
}

/* Decryption
 * A function which takes the cipher text, the private key and a number n which
 * is he product of p and q. The function decrypts the cipher text using the
 * private key and returns the decrypted message.
 */

char *decrypt(char *cipher, long d, long n) {
  long i;
  long len = strlen(cipher);
  char *message = (char *)malloc(len * sizeof(char));
  for (i = 0; i < len; i++) {
    // message[i] = (long) pow(cipher[i], d) % n;
    message[i] = pomod(cipher[i], d, n);
    printf("\n%c -> %c", cipher[i], message[i]);
  }
  return message;
}

int main() {
  int num;
  int p, q, lambda_n;
  long n, e, d;
  char *message;
  char *cipher;
  printf("\nEnter the value of p: ");
  scanf("%d", &p);
  printf("\nEnter the value of q: ");
  scanf("%d", &q);
  if (isPrime(p) && isPrime(q)) {
    n = p * q;
    lambda_n = totient(p, q);
    e = randome(lambda_n);
    d = private_key(e, lambda_n);
    printf("\nThe value of n is %ld", n);
    printf("\nThe value of lambda_n is %d", lambda_n);
    printf("\nThe value of e is %ld", e);
    printf("\nThe value of d is %ld", d);
    printf("\nenter 1 to encrypt message or 2 to decrypt:");
    scanf("%d",&num);
    if(num==1)
    {
    printf("\nEnter the message: ");
    message = (char *)malloc(sizeof(char) * 100);
    scanf("%s", message);
    cipher = encrypt(message, e, n);
    puts("\nThe encrypted message is: ");
    printf("%s", cipher);
    }
    else if(num==2)
    {
    printf("\nEnter the message: ");
    cipher = (char *)malloc(sizeof(char) * 100);
    scanf("%s", cipher);
    message = decrypt(cipher, d, n);
    puts("\nThe decrypted message is:");
    printf("%s", message);
  }
  }
  else {
    printf("\nThe value of p and q should be prime.");
  }
  return 0;
}
