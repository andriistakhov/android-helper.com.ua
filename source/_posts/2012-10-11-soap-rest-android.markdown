---
author: andrew
comments: true
date: 2012-10-11 08:14:09+00:00
layout: post
slug: soap-rest-android
title: SOAP и REST web-сервиса в Android
wordpress_id: 742
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Добрый день друзья.





Продолжаю собирать важную информацию по программированию под android.





Сегодня речь пойдет о SOAP и REST запросах.


 <!-- more -->


Одной из задач, часто решаемых разработчиками мобильного софта, является получение данных от удаленного **web сервиса**. Этот процесс включает в себя несколько этапов: отправка запрос web-сервису, получение ответа и парсинг ответа с целью получения данных. В Android нет встроенного SOAP клиента, однако существует несколько довольно неплохих сторонних библиотек. Мы не будем пользоваться чужими поделками, а разработаем собственный клиент на базе Android API.  
  
_**SOAP **(Simple Object Access Protocol) это основанный на XML протокол, предназначенный для обмена данными между распределенными приложениями.  **REST **(Representational State Transfer)- архитектура позволяющая строить распределенные, масштабируемые приложения. Основная задача сервера в этой архитектуре предоставить клиентам доступ к ресурсам по их идентификаторам (URI). Под доступом подразумевается как получение информации, так и ее изменение. SOAP и REST строятся поверх существующих web протоколов, например HTTP. Я не буду здесь вдаваться в детали, если Вы не знакомы с темой, рекомендую посмотреть вот эту [статью](http://habrahabr.ru/blogs/webdev/131343/)._  
  






### Формат запроса для SOAP сервису





Прежде чем переходить к написанию кода, давайте посмотрим на структуру SOAP.





![](http://android-helper.com.ua/images/uploads/2012/10/soap_structure.png)





Обычный** SOAP запрос** выглядит так:




	POST /InStock HTTP/1.1

	Host: www.example.org

	Content-Type: application/soap+xml; charset=utf-8

	Content-Length: length

	SOAPAction: "http://www.w3schools.com/GetItems"

 

	<?xml version="1.0"?>

	<soap:Envelope

	xmlns:soap="http://www.w3.org/2001/12/soap-envelope"

	soap:encodingStyle="http://www.w3.org/2001/12/soap-encoding">

	<soap:Header>

  	<m:Trans xmlns:m="http://www.w3schools.com/transaction/"

  	soap:mustUnderstand="1">234

	  </m:Trans>

	</soap:Header>

	<soap:Body>

  	<m:GetPrice xmlns:m="http://www.w3schools.com/prices">

    	<m:Item>Apples</m:Item>

	  </m:GetPrice>

	</soap:Body></soap:Envelope>




То есть SOAP запрос/ответ передается как пакет (SOAP Envelope), состоящий из заголовка (SOAP Header) и тела (SOAP Body).**SOAP Header** - вспомогательный  компонент, содержащий конфиденциальную информацию для приложения, например параметры  аутентификации. **SOAP Body** - это сообщение с полезной информацией. Заголовок может также содержать** SOAP Action**, который определяет функцию, запрашиваемую сервисом.  
  






### Отправка запроса SOAP-сервису





Чтобы вызвать SOAP-сервис вы должны сделать две вещи:  
**Во-первых**, вручную сконструировать SOAP пакет, например вот так:




	String envelope="<?xml version=\"1.0\" encoding=\"utf-8\"?>"+

	"<soap:Envelope xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\"xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\"xmlns:soap=\"http://schemas.xmlsoap.org/soap/envelope/\">"+

  	"<soap:Body>"+

    "<GetItems xmlns=\"http://tempuri.org/\">"+

      "<startDate>%s</ startDate>"+

      "<getAll>%s</getAll>"+

    "</Items>"+

  	"</soap:Body>"+

	"</soap:Envelope>";




где %s метка формата. С помощью метода String.format на ее место  будет подставляться конкретное значение.


	String requestEnvelope=String.format(envelope, "10-5-2011","true");



**Во-вторых**, вызвать web-сервис, например так:

	String CallWebService(String url,

    String soapAction,

   	String envelope)  {

  	final DefaultHttpClient httpClient=new DefaultHttpClient();

	  // параметры запроса

	  HttpParams params = httpClient.getParams();

     HttpConnectionParams.setConnectionTimeout(params, 10000);

     HttpConnectionParams.setSoTimeout(params, 15000);

     // устанавливаем параметры

  	HttpProtocolParams.setUseExpectContinue(httpClient.getParams(), true);

 

  	// С помощью метода POST отправляем конверт

  	HttpPost httppost = new HttpPost(url);

  	// и заголовки

     httppost.setHeader("soapaction", soapAction);

     httppost.setHeader("Content-Type", "text/xml; charset=utf-8");

 

     String responseString="";

     try {

 

      // выполняем запрос

   	HttpEntity entity = new StringEntity(envelope);

   	httppost.setEntity(entity);

 

   	// Заголоаок запроса

   	ResponseHandler<string> rh=new ResponseHandler<string>() {

    // вызывается, когда клиент пришлет ответ

    public String handleResponse(HttpResponse response)

      throws ClientProtocolException, IOException {

 

     // получаем ответ

     HttpEntity entity = response.getEntity();

 

     // читаем его в массив

           StringBuffer out = new StringBuffer();

           byte[] b = EntityUtils.toByteArray(entity);

 

           // write the response byte array to a string buffer

           out.append(new String(b, 0, b.length));

           return out.toString();

    }

   	};

 

   	responseString=httpClient.execute(httppost, rh); 

 

  	}

     catch (Exception e) {

      Log.v("exception", e.toString());

  	}

 

     // закрываем соединение

  	httpClient.getConnectionManager().shutdown();

  	return responseString;

 		}




После вызова этой функции Вы получите ответ, который будет выглядеть как-то так:


	<?xml version="1.0" encoding="utf-8"?>

	<soap:Envelope xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">

  	<soap:Body>

    	<GetItemsResponse xmlns="http://tempuri.org/">

     	 <GetItemsResult>



        <Items>

          <Item>

            <name>string</name>

            <description>string</ description >

          </iPhoneCategory>

          <iPhoneCategory>

            <name>string</name>

            <description>string</ description >

          </ Item >

        </Items>

      	</GetItemsResult>

    	</ GetItemsResponse >

  	</soap:Body>

	</soap:Envelope>


Этот ответ нужно разобрать XML парсером и извлечь нужные данные.  
  






### Отправка запроса REST-сервису





Расбота с REST-сервисом намного проще. Вы должны вызвать сервис, передав ему URL с параметрами, например вот так:  
  
	http://example.com/resources/getitems  
  
Пример вызова REST сервиса:


	String callWebErvice(String serviceURL){

        // http get client

            HttpClient client=new DefaultHttpClient();

            HttpGet getRequest=new HttpGet();

 

            try {

                // создаем a URI объект

                getRequest.setURI(new URI(serviceURL));

            } catch (URISyntaxException e) {

                Log.e("URISyntaxException", e.toString());

            }

 

            // Создаем BufferedReader дял чтения ответа

            BufferedReader in=null;

            // и HttpResponse для получения ответа

            HttpResponse response=null;

            try {

                // выполняем запрос

                response = client.execute(getRequest);

            } catch (ClientProtocolException e) {

                Log.e("ClientProtocolException", e.toString());

            } catch (IOException e) {

                Log.e("IO exception", e.toString());

            }

            try {

                in=new BufferedReader(new InputStreamReader(response.getEntity().getContent()));

            } catch (IllegalStateException e) {

                Log.e("IllegalStateException", e.toString());

            } catch (IOException e) {

                Log.e("IO exception", e.toString());

            }

            StringBuffer buff=new StringBuffer("");

            String line="";

            try {

                while((line=in.readLine())!=null)

                {

                    buff.append(line);

                }

            } catch (IOException e) {

                Log.e("IO exception", e.toString());

                return e.getMessage();

            }

 

            try {

                in.close();

            } catch (IOException e) {

                Log.e("IO exception", e.toString());

            }

            // возвращаем ответ в виде строки текста

            return buff.toString();

    }




Соединение с web-сервисом по протоколу Secure Sockets Layer (SSL)  
  
HttpClinet не поддерживает SSL соединения, поэтому если Вам необходимо работать с защищенным web-сервисом, то используйте  javax.net.ssl.HttpsURLConnection. Ниже приведен пример, иллюстрирующий работу с SSL SOAP web-сервисом.


	String CallWebService(String url,

             String soapAction,

            String envelope) throws IOException  {

        URL address=new URL(url);

        URLConnection connection=address.openConnection();

        HttpsURLConnection post=(HttpsURLConnection)connection;

        post.setDoInput(true);

        post.setDoOutput(true);

        post.setRequestMethod("POST");

        post.setRequestProperty("SOAPAction", soapAction);

        post.setRequestProperty( "Content-type", "text/xml; charset=utf-8" );

        post.setRequestProperty( "Content-Length", String.valueOf(envelope.length()));

        post.setReadTimeout(4000);

 

        OutputStream outStream=post.getOutputStream();

        Writer out=new OutputStreamWriter(outStream);

        out.write(envelope);

        out.flush();

        out.close();

 

        InputStream inStream = post.getInputStream();

        BufferedInputStream in = new BufferedInputStream(inStream,4);

        StringBuffer buffer=new StringBuffer();

        // читаем за раз 4 байта

        byte[] buffArray=new byte[4];

        int c=0;

            while((c=in.read(buffArray))!=-1){

                for(int i=0;i<c;i++)

                    buffer.append((char)buffArray[i]);

            }

 

            return buffer.toString();

    }


Итак, мы отправили запрос web-сервису и получили ответ в виде строки. Настало время использовать парсер.  
  






### Использование парсера в Android





В зависимости от реализации сервиса в ответ на наш запрос может прийти ответ в формате XML, SOAP или JSON.  
  






#### Программирование XML парсера в Android





Рассмотрим конкретный пример. Допустим, нам пришел XML ответ, содержащий имя, фамилию и возраст пользователя:


	<?xml version="1.0"?>

	<person>

    <firstname>Jack</firstname>

    <lastname>smith</lastname>

    <age>28</age>

	</person>





С помощью этих параметров нужно создать объект класса Person:




	public class Person{

        public String firstName;

        public String lastName;

        public int age;

    }


### Использование DOM Parser





В библиотека **org.w3c.dom** можно найти классы, с помощью которых можно разобрать xml, создав документ и сравнив каждый узел с шаблоном. Приведенная ниже функция использует DOM парсер:



	void parseByDOM(String response) throws 	ParserConfigurationException, SAXException, IOException{

        Person person=new Person();

        DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();

        DocumentBuilder db = dbf.newDocumentBuilder();

        Document doc = db.parse(new InputSource(new StringReader(response)));

        // нормализируем документ

        doc.getDocumentElement().normalize();

        // получаем корневой узел

        NodeList nodeList = doc.getElementsByTagName("person");

        Node node=nodeList.item(0);

        // узел имеет три дочерних узла

        for (int i = 0; i < node.getChildNodes().getLength(); i++) {

        Node temp=node.getChildNodes().item(i);

        if(temp.getNodeName().equalsIgnoreCase("firstname")){

            person.firstName=temp.getTextContent();

        }

        else if(temp.getNodeName().equalsIgnoreCase("lastname")){

            person.lastName=temp.getTextContent();

        }

        else if(temp.getNodeName().equalsIgnoreCase("age")){

            person.age=Integer.parseInt(temp.getTextContent());

        }

 

        }

 

        Log.e("person", person.firstName+ " "+person.lastName+" "+String.valueOf(person.age));

    }




Показаный метод работает, однако для его использования нужно знать структуру разбираемого xml и порядок перечисления узлов. Более гибок в этом плане SAX парсер.  
 





###  Использование SAX Parser





SAX Parser находится в пакете **org.xml.sax**. В процессе разбора документа SAX генерирует события, для которых программист должен написать обработчики. Создадим класс-потомок основе класса **DefaultHandler **и переопределим следующие методы:  
 








  * **startDocument()**: Этот метод вызывается при открытии XML документа.



  * **startElement(String uri, String localName, String qName, Attributes attributes)**: вызывается, когда парсер встречает XML-узел.



  * **endElement(String uri, String localName, String Name)**: вызывается, когда парсер считал закрывающий тег.



  * **characters(char[] ch, int start, int length)**: этот метод вызывается, когда парсер считывает данные из узла.






Напишем класс для парсинга нашего примера





	public class PersonParser extends DefaultHandler

	{

 

    // arraylist для сохранения объектов persons

    ArrayList persons;

    // вспомогательный объект

    Person tempPerson;

    // буфер

    StringBuilder builder;

 

    /**

     * Инициализируем arraylist

     * @throws SAXException

     */

    @Override

    public void startDocument() throws SAXException {

        pesons=new ArrayList();

 

    }

 

    /**

     * Инициализируем вспомогательный объект и буфер 

     * @param uri

     * @param localName

     * @param qName

     * @param attributes

     * @throws SAXException

     */

    @Override

    public void startElement(String uri, String localName, String qName,

            Attributes attributes) throws SAXException {

 

        if(localName.equalsIgnoreCase.equals("person")){

            tempPerson=new Person();

            builder=new StringBuilder();

        }

 

    }

    /**

     * Завершение чтения тега person и

     * добавление данных в arraylist

     * @param uri

     * @param localName

     * @param qName

     * @throws SAXException

     */

    @Override

    public void endElement(String uri, String localName, String qName)

            throws SAXException {

        // закончилось чтение person, добавим собранные данные в массив

        if(localName.toLowerCase().equals("person"))

        {

            this.persons.add(tempPerson);

        }

        // закончилось чтение  "firstname", добавим эти данные во вспомогательный объект

        else if(localName.toLowerCase().equals("firstname")){

            tempPerson.firstName=builder.toString();

        }

        // закончилось чтение  "lastname", добавим эти данные во вспомогательный объект

        else if(localName.toLowerCase().equals("lastname")){

            tempPerson.lastName=builder.toString();

        }

        // закончилось чтение  "age", добавим эти данные во вспомогательный объект

        else if(localName.toLowerCase().equals("age")){

            tempPerson.age=Integer.parseInt(builder.toString());

        }

    }

 

    /**

     * Считываем данные из каждого тега

     * @param ch

     * @param start

     * @param length

     * @throws SAXException

     */

    @Override

    public void characters(char[] ch, int start, int length)

            throws SAXException {

        // читаем символы в буфер

        String tempString=new String(ch, start, length);

         builder.append(tempString);

    }

	}



Код довольно прост. Парсер проходится по всем узлам, и в зависимости от считанного тега, вы выполняете то или иное действие. Используем этот класс:


	public ArrayList getPersons(final String response) throws 

	ParserConfigurationException, SAXException, IOException

    {

        BufferedReader br=new BufferedReader(new StringReader(response));

        InputSource is=new InputSource(br);

        PersonParser parser=new PersonParser();

        SAXParserFactory factory=SAXParserFactory.newInstance();

           SAXParser sp=factory.newSAXParser();

           XMLReader reader=sp.getXMLReader();

           reader.setContentHandler(parser);

           reader.parse(is);

           ArrayList persons=parser.persons;

 

        return persons;

 

    }




### Программирование JSON парсера в Android





Некоторые сервисы могут вернуть ответ не в виде XML, а как JSON массив:



	"persons"

	[

	{

		"person"{

			"firstName": "John",

     	"lastName": "Smith",

    	"age": 25

		}

	}

	{

		"person"{

			"firstName": "Catherine",

     	"lastName": "Jones",

     	"age": 35

		}

	}

	]





Здесь мы видим JSON массив persons, содержащий несколько JSON объектов person. На самом деле, работать с JSON намного проще, чем с XML:

	public ArrayList<Person> getMessage(String response){

        JSONObject jsonResponse;

        ArrayList<Person> arrPersons=new ArrayList<Person>;

        try {

            // получаем ответ

            jsonResponse = new JSONObject(response);

            // получаем массив

            JSONArray persons=jsonResponse.optJSONArray("persons");

            // проходимся по массиву и извлекаем персоны

            for(int i=0;i<persons.length();i++){

                // получаем объект person

                JSONObject person=persons.getJSONObject(i);

                // получаем firstname

                String firstname=person.optString("firstname");

                // получаем lastname

                String lastname=person.optString("lastname");

                // получаем age

                int age=person.optInt("age");

 

                // создаем объект и добавляем его в массив

                Person p=new Person();

                p.firstName=firstname;

                p.lastName=lastname;

                p.age=age;

                arrPersons.add(p);

            }

 

        } catch (JSONException e) {

 

            e.printStackTrace();

        }

 

        return arrPersons;

    }    



Обратите внимание, здесь мы использовали методы optJSONArray, optString, optInt вместо getString, getInt, поскольку opt-методы эти методы возвращают пустую строку или 0, если какой-то элемент не найден, в то время как get-методы генерируют исключения.





Источник статьи [mobilab.ru](http://www.mobilab.ru/androiddev/androidwebserviceclient.html)
