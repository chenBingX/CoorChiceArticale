
[官方 TransactionTooLargeException 说明](https://developer.android.com/reference/kotlin/android/os/TransactionTooLargeException.html?hl=zh-cn)

> The Binder transaction buffer has a limited fixed size, currently 1Mb, which is shared by all transactions in progress for the process. Consequently this exception can be thrown when there are many transactions in progress even when most of the individual transactions are of moderate size


```
      case FAILED_TRANSACTION: {

            ALOGE("!!! FAILED BINDER TRANSACTION !!!  (parcel size = %d)", parcelSize);

            const char* exceptionToThrow;

            char msg[128];

            // TransactionTooLargeException is a checked exception, only throw from certain methods.

            // FIXME: Transaction too large is the most common reason for FAILED_TRANSACTION

            //        but it is not the only one.  The Binder driver can return BR_FAILED_REPLY

            //        for other reasons also, such as if the transaction is malformed or

            //        refers to an FD that has been closed.  We should change the driver

            //        to enable us to distinguish these cases in the future.

            if (canThrowRemoteException && parcelSize > 200*1024) {

                // bona fide large payload

                exceptionToThrow = "android/os/TransactionTooLargeException";

                snprintf(msg, sizeof(msg)-1, "data parcel size %d bytes", parcelSize);

            } else {

                // Heuristic: a payload smaller than this threshold "shouldn't" be too

                // big, so it's probably some other, more subtle problem.  In practice

                // it seems to always mean that the remote process died while the binder

                // transaction was already in flight.

                exceptionToThrow = (canThrowRemoteException)

                        ? "android/os/DeadObjectException"

                        : "java/lang/RuntimeException";

                snprintf(msg, sizeof(msg)-1,

                        "Transaction failed on small parcel; remote process probably died");

            }

            jniThrowException(env, exceptionToThrow, msg);

        } break;
```