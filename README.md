# opencsv
Automatically exported from code.google.com/p/opencsv. It is fully backward compatible with orignal version of OpenCSV 2.3.0.
This project is fork of OpenCSV project and was created by dsana...@gmail.com (dsanatin). As GitHub is shutting down I (kulatamicuda as a user of this forked library) mirrored whole bunch of code from dsanatin here.


**The purpose of this project is solve the following problems of old version OpenCSV:**

1. CSVWriter & CSVReader constructors have too many arguments
2. Usage of checked exceptions
3. CSVWriter & CSVReader should be closed to release resources

**The old version:**
```
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.util.Arrays;

public class Before {
        
        public static void main(String[] args) {
                String fileName = "test.csv";
                try {
                        // duplicate full set of settings of CSV file format
                        CSVWriter writer = new CSVWriter(new OutputStreamWriter(
                                        new FileOutputStream(fileName), "UTF-8"),
                                        ';', '\'');
                        try {
                                // we have to create arrays manually
                                writer.writeNext(new String[] {"Header1", "Header2"});
                                writer.writeNext(new String[] {"v11", "v12"});
                                writer.writeNext(new String[] {"v21", "v22"});
                        } finally {
                                // we have to close writer manually
                                writer.close(); 
                        }
                } catch (IOException e) {
                        // we have to process exceptions when it is not required
                        e.printStackTrace();
                }

                try {
                        // duplicate full set of settings of CSV file format
                        CSVReader reader = new CSVReader(new InputStreamReader(
                                        new FileInputStream(fileName), "UTF-8"), 
                                        ';', '\'', 1); // it is not clear what arguments means 
                        try {
                                
                                String[] values = reader.readNext();
                                while ( values != null ) {
                                        System.out.println(Arrays.asList(values));
                                        values = reader.readNext();
                                }
                        } finally {
                                // we have to close reader manually
                                reader.close();
                        }
                } catch (IOException e) {
                        // we have to process exceptions when it is not required
                        e.printStackTrace();
                }
        }
}
```
**The new version:**
```
package au.com.bytecode.opencsv;

import java.util.Arrays;

public class CSVAfter {
  // define format of CSV file one time and use everywhere
  // human readable configuration
  private static final CSV csv = CSV.separator(';').quote('\'').skipLines(1)
      .charset("UTF-8").create();

  // do not throw checked exceptions, use Java 8 features
  public static void main(String[] args) {
    String fileName = "test.csv";

    // CSVWriter will be closed after end of processing
    csv.write(fileName, out -> {
      out.writeNext("Header1", "Header2");
      out.writeNext("v11", "v12");
      out.writeNext("v21", "v22");
    });

    // CSVReader will be closed after end of processing
    csv.read(fileName, (rowIndex, values) -> {
      System.out.println(rowIndex + "# " + Arrays.asList(values));
    });
  }
}
```
