# R30XSeriesFingerprintApi
 /////////////////////Create ///////////////////////////////////////
            var fingerPrint = new FingerPrint(new byte[] { 0xFF, 0xFF, 0xFF, 0xFF }, new WinIotPakcgeManager());
            //Verify Password   defult password is 0000   return trye or false
            var verify = fingerPrint.VerifyPassword(new byte[] { 0x0, 0x0, 0x0, 0x0 });
            if (! await verify) return;
            //*****************************************************************************************************
            //Enrol
            //Scan finger While Correct Scan Break  correct scan return zero
            //Scaned Finger save on device main memory
            while (true)
            {
               if( await fingerPrint.GenerateImage()==0)
                    break;
            }
            //After convert scanned image to data for store  with Generate to tz
            //To generate character file from the original finger image in ImageBuffer and store the file in CharBuffer1(0x1) or CharBuffer2(0x2)
            await fingerPrint.GenerateImageToTz(0x1);
            var pageNumber = 1; //integer and 4 byte
            //page ID 1 - 1001 for r305
            var pageNumberBytes = BitConverter.GetBytes(pageNumber);// byte array with lenght 4  becuse integer is 4 byte
            //page number address is 2 byte and use only to byte of pageNumberBytes
            //befor reverse index 0 and index 1
            await fingerPrint.StoreTemplate(0x1, new byte[] {pageNumberBytes[1], pageNumberBytes[0]});
            //Enrol
            //*****************************************************************************************************
            /////////////////////Search ///////////////////////////////////////
            //Scan finger While Correct Scan Break  correct scan return zero
            //Scaned Finger save on device main memory
            while (true)
            {
                if (await fingerPrint.GenerateImage() == 0)
                    break;
            }
            //After convert scanned image to data for store  with Generate to tz
            //To generate character file from the original finger image in ImageBuffer and store the file in CharBuffer1(0x1) or CharBuffer2(0x2)
            await fingerPrint.GenerateImageToTz(0x1);
            await fingerPrint.HighSearchFinger(0x1, new byte[] {0x0, 0x0}, new byte[] {0x3, 0xe9}); //start from 0 to 1001{3e9}
                                                                                                    //HighSearchFinger Method Rerurn Page ID
                                                                                                    //*****************************************************************************************************
            /////////////////////Delete ///////////////////////////////////////
            var pageNumberDelete = 1; //integer and 4 byte
            //page ID 1 - 1001 for r305
            var pageNumberDeleteBytes = BitConverter.GetBytes(pageNumberDelete);// byte array with lenght 4  becuse integer is 4 byte
            //page number address is 2 byte and use only to byte of pageNumberBytes
            //befor reverse index 0 and index 1
            await fingerPrint.DeleteTemplate(new byte[] {pageNumberDeleteBytes[1], pageNumberDeleteBytes[0]},
                new byte[] {0x0, 0x1});
            //byte[] {0x0, 0x1} Template Lenght is 1
            //return true if deleted
            //*****************************************************************************************************
