import java.io.*;
import java.util.*;
import java.util.regex.*;
import java.io.InputStream;
import  org.xml.sax.ContentHandler;
import org.apache.tika.metadata.Metadata;
import org.apache.tika.sax.*;
import org.apache.tika.parser.ParseContext;
import org.apache.tika.parser.pdf.*;



public class hw4 {

	List<String> keywords;
	PrintWriter logfile;
	int num_keywords, num_files, num_fileswithkeywords;
	Map<String,Integer> keyword_counts;
	Date timestamp;

	/**
	 * constructor
	 * DO NOT MODIFY
	 */
	public hw4() {
		keywords = new ArrayList<String>();
		num_keywords=0;
		num_files=0;
		num_fileswithkeywords=0;
		keyword_counts = new HashMap<String,Integer>();
		timestamp = new Date();
		try {
			logfile = new PrintWriter("log.txt");
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		}
	}

	/**
	 * destructor
	 * DO NOT MODIFY
	 */
	protected void finalize() throws Throwable {
		try {
			logfile.close();
	    } finally {
	        super.finalize();
	    }
	}

	/**
	 * main() function
	 * instantiate class and execute
	 * DO NOT MODIFY
	 */
	public static void main(String[] args) {
		hw4 instance = new hw4();
		instance.run();
	}

	/**
	 * execute the program
	 * DO NOT MODIFY
	 */
	private void run() {

		// Open input file and read keywords
		try {
			BufferedReader keyword_reader = new BufferedReader(new FileReader("keywords.txt"));
			String str;
			while ((str = keyword_reader.readLine()) != null) {
				keywords.add(str);
				num_keywords++;
				keyword_counts.put(str, 0);
			}
			keyword_reader.close();
		} catch (IOException e) {
			e.printStackTrace();
		}

		// Open all pdf files, process each one
		File pdfdir = new File("./vault2");
		File[] pdfs = pdfdir.listFiles(new PDFFilenameFilter());
		for (File pdf:pdfs) {
			num_files++;
			try {
				processfile(pdf);

			} catch (FileNotFoundException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}

		// Print output file
		try {
			PrintWriter outfile = new PrintWriter("output.txt");
			outfile.print("Keyword(s) used: ");
			if (num_keywords>0) outfile.print(keywords.get(0));
			for (int i=1; i<num_keywords; i++) outfile.print(", "+keywords.get(i));
			outfile.println();
			outfile.println("No of files processed: " + num_files);
			outfile.println("No of files containing keyword(s): " + num_fileswithkeywords);
			outfile.println();
			outfile.println("No of occurrences of each keyword:");
			outfile.println("----------------------------------");
			for (int i=0; i<num_keywords; i++) {
				String keyword = keywords.get(i);
				outfile.println("\t"+keyword+": "+keyword_counts.get(keyword));
			}
			outfile.close();
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		}
	}

	/**
	 * Process a single file
	 * 
	 * Here, you need to:
	 *  - use Tika to extract text contents from the file
	 *  - (optional) check OCR quality before proceeding
	 *  - search the extracted text for the given keywords
	 *  - update num_fileswithkeywords and keyword_counts as needed
	 *  - update log file as needed
	 * 
	 * @param f File to be processed
	 * @throws FileNotFoundException 
	 */
	private void processfile(File f) throws FileNotFoundException {
		
	try {
			InputStream input = new FileInputStream(f);
			//System.out.println(f.getName());
			ContentHandler textHandler = new BodyContentHandler(60*1024*1024);
			Metadata metadata = new Metadata();
			ParseContext context = new ParseContext();
			PDFParser parser = new PDFParser();
		
			//PrintWriter pdfFile = new PrintWriter(f.getName()+".txt");
		
			//parse pdf file
			parser.setEnableAutoSpace(true);
			parser.parse(input, textHandler, metadata, context);
			String string = new String(textHandler.toString());
			//Scanner scan = new Scanner(string);
			String[] token = string.split("");
			int k = 0;
			
			HashMap<String, Integer> listOfWords = new HashMap<String, Integer>();
			//String word = scan.next(); //scanner automatically uses " " as a delimeter
			
			for(k = 0 ; k < token.length ; k ++)
			{
				int countWord = 0;
	        
				if(!listOfWords.containsKey(token[k]))
				{ //add word if it isn't added already
	        	
					listOfWords.put(token[k], 1); //first occurance of this word
	            
				}
				else
				{
					countWord = listOfWords.get(token[k]) + 1; //get current count and increment
					//now put the new value back in the HashMap
					listOfWords.remove(token[k]); //first remove it (can't have duplicate keys)
					listOfWords.put(token[k], countWord); //now put it back with new value
				}
					
			}
			int size = listOfWords.size();
			String uniqueWord = listOfWords.get(token[k]).toString();
			System.out.println("size is = " + size + "and uniqueWord is = " + uniqueWord );	
			
			
	        //pdfFile.println(string);
			boolean  num_fileswithkeywordsFlag = false;
			// Instantiate a Date object
		    //Date date = new Date();
			
			for(int i = 0 ; i<keywords.size(); i++)
			{	
				int count = 0;
				boolean firstFlag = true;
				//Step1: take one keyword from the text file
				String keyword = new String(keywords.get(i));
				//System.out.println("keyword is "+keyword);
				//String REGEX = "\\b"+ keyword +"\\b";
				String delims = "[ ]+";
				String[] tokens = keyword.split(delims);
				String REGEX = "\\b";
				for(int j=0 ; j<tokens.length ; j++)
				{
					if(true == firstFlag )
					{
						//System.out.println(tokens[j]);
						//allocate  REGEX
						REGEX = REGEX +tokens[j] + "\\b";
						//System.out.println(REGEX);
						firstFlag = false;
					}
					else
					{
						REGEX = REGEX + "[ ]*\\b"+tokens[j] + "\\b";
						//System.out.println(REGEX);
					}
					
				}
				
				//find the number of matches
				final Pattern p1 = Pattern.compile(REGEX, Pattern.CASE_INSENSITIVE);
				// get a matcher object
			    Matcher m = p1.matcher(string);
			    while (m.find())
			    {
			        count++;
			        num_fileswithkeywordsFlag = true;
			        // display time and date using toString()
			        //logfile.println("keyword = '"+keyword+"'   PDF file name = '" +f.getName()+"'  Timestamp ="+ date.toString());
			        updatelog(keyword, f);
			    
			    }
			    
			    //System.out.println("count for "+keyword +" is " + count);
			    keyword_counts.put(keyword,keyword_counts.get(keyword)+count);
			    
			}
			
			if(true == num_fileswithkeywordsFlag)
		    {
		    	num_fileswithkeywords++;
		    	//System.out.println("num_fileswithkeywords is " +num_fileswithkeywords);
		    }
			
			input.close();
			logfile.flush();
			//logfile.close();
				
			} //catch (SAXException | TikaException  | IOException | Exception e) {
			catch (Exception e){
				// TODO Auto-generated catch block
				e.printStackTrace();
				
			}
			
	
	
		// to update the log file with a search hit, use:
		// 	updatelog(keyword,f.getName());

	}

	/**
	 * Update the log file with search hit
	 * Appends a log entry with the system timestamp, keyword found, and filename of PDF file containing the keyword
	 * DO NOT MODIFY
	 */
	//@SuppressWarnings("unused")
	private void updatelog(String keyword, File filename) {
		timestamp.setTime(System.currentTimeMillis());
		logfile.println(timestamp + " -- \"" + keyword + "\" found in file \"" + filename +"\"");
		logfile.flush();
	}

	/**
	 * Filename filter that accepts only *.pdf
	 * DO NOT MODIFY 
	 */
	static class PDFFilenameFilter implements FilenameFilter {
		private Pattern p = Pattern.compile(".*\\.pdf",Pattern.CASE_INSENSITIVE);
		public boolean accept(File dir, String name) {
			Matcher m = p.matcher(name);
			return m.matches();
		}
	}
}
