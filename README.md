# SeleniumLearning
import org.junit.jupiter.api.*;
import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
public class FirstTest {

   ChromeDriver driver;
    WebDriverWait wait;

    String goodvoucher = "10procent";
    String badvaucher = "20procent";
    By windsurfingGroup = By.cssSelector("a[href*='category/windsurfing']");
    By cookieConsentBar = By.cssSelector("a[class*='dismiss-link']");
    By produkt = By.cssSelector("a.button[href*='?add-to-cart=386']");
    By kokszyk = By.cssSelector("a.cart-contents");
    By klikalny = By.cssSelector("a.added[href*='?add-to-cart=386']");
    By zastosujkupon = By.cssSelector("button[value='Zastosuj kupon']");
    By redalert = By.cssSelector("ul[role='alert']");
    By greenalert = By.cssSelector("div[role='alert']");
    String productbeforebasket = "Egipt - El Gouna";
    By productinbasket = By.cssSelector("td[data-title='Produkt'] ");
    @BeforeEach
    public void driverSetup() {
        System.setProperty("webdriver.chrome.driver", "src/main/resources/chromedriver.exe");
//       System.setProperty("javax.net.ssl.trustStore","E:/IT/Java/Pliki/CERT.cer");
        ChromeOptions options = new ChromeOptions();
        options.addArguments("incognito");
//        options.addArguments("ignore-certificate-errors");
        options.setAcceptInsecureCerts(true);
        driver = new ChromeDriver(options);
        driver.manage().window().setSize(new Dimension(1295, 760));
        driver.manage().window().setPosition(new Point(8, 30));
        driver.navigate().to("http://fakestore.testelka.pl/shop/");

        wait = new WebDriverWait(driver, 10);

        driver.findElement(cookieConsentBar).click();
        driver.findElement(windsurfingGroup).click();
        wait.until(ExpectedConditions.elementToBeClickable(produkt)).click();
        wait.until(ExpectedConditions.visibilityOfElementLocated(klikalny));
        driver.findElement(kokszyk).click();
    }

    @AfterEach
    public void driverQuit() {
        driver.close();
        driver.quit();
    }
    @Test
    public void checkproduct() {
        Assertions.assertEquals(productbeforebasket,driver.findElement(productinbasket).getText(),"Do koszyka trafił nie ten produkt");

    }
    @Test
    public void goodVoucher() {
        getGoodVoucher();
        Assertions.assertEquals("Kupon został pomyślnie użyty.", alertText(true), "Czy kupon jest poprawny?");
    }

    @Test
    public void badVoucher() {
        getBadVoucher();
        Assertions.assertEquals("Kupon \"" + badvaucher + "\" nie istnieje!", alertText(false), "Ten kupon powinien być nieaktywny.");
    }

    private void getGoodVoucher() {
        driver.findElement(By.cssSelector("input[id='coupon_code']")).sendKeys(goodvoucher);
        driver.findElement(zastosujkupon).click();
    }

    private void getBadVoucher() {
        driver.findElement(By.cssSelector("input[id='coupon_code']")).sendKeys(badvaucher);
        driver.findElement(zastosujkupon).click();

    }

    private String alertText(Boolean ifproper) {
        if (ifproper) {
            wait.until(ExpectedConditions.visibilityOfElementLocated(greenalert));
            WebElement GreenNotification = driver.findElement(greenalert);
                return GreenNotification.getText();
        } else {
            wait.until(ExpectedConditions.visibilityOfElementLocated(redalert));
            WebElement RedNotification = driver.findElement(redalert);
                return RedNotification.getText();
        }


    }


}
