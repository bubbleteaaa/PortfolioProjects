-- ALTER TABLE PortfolioProject..CovidVaccinations
-- ALTER COLUMN new_vaccinations bigint;

SELECT * 
FROM PortfolioProject..CovidVaccinations

SELECT *
FROM PortfolioProject..CovidDeaths
WHERE continent IS NOT NULL
ORDER BY 3,4

-- Select data that we are going to be working with
-- order based on column number
SELECT location, date, total_cases, new_cases, total_deaths, population
FROM PortfolioProject..CovidDeaths
ORDER by 1,2

-- looking at the total cases vs total deaths
-- shows the likelihood of dying if covid is contracted in Canada
SELECT location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 AS DeathPercentage
FROM PortfolioProject..CovidDeaths
WHERE location = 'Canada'
ORDER by 1,2

-- Looking at the total cases vs population
-- shows what percentage of population in Canada got Covid
SELECT location, date,  population, total_cases, (total_cases/population)*100 AS 
    PercentOfPopulationInfected
FROM PortfolioProject..CovidDeaths
WHERE location = 'Canada'
ORDER by 1,2

-- Looking at countries with highest infection rates compared to populations 
SELECT location, population, MAX(total_cases) AS HighestInfectionCount, MAX((total_cases/population))*100 AS 
    PercentOfPopulationInfected
FROM PortfolioProject..CovidDeaths
--WHERE location = 'Canada'
GROUP BY location, population
ORDER by PercentOfPopulationInfected DESC

-- Showing the countries with the Highest Death Count per Population
SELECT location, MAX(cast(total_deaths AS int)) AS TotalDeathCount
FROM PortfolioProject..CovidDeaths
--WHERE location = 'Canada'
WHERE continent IS NOT NULL
GROUP BY location
ORDER by TotalDeathCount DESC

-- Showing the contries with the highest death count

SELECT location, MAX(cast(total_deaths AS int)) AS TotalDeathCount
FROM PortfolioProject..CovidDeaths
--WHERE location = 'Canada'
WHERE continent IS NOT NULL
GROUP BY location
ORDER by TotalDeathCount DESC

-- Breaking things down by continent

-- Showing the continents with the highest death count

SELECT continent, MAX(cast(total_deaths AS int)) AS TotalDeathCount
FROM PortfolioProject..CovidDeaths
--WHERE location = 'Canada'
WHERE continent IS NOT NULL
GROUP BY continent
ORDER by TotalDeathCount DESC

-- Global Numbers

SELECT date, SUM(new_cases) AS total_cases, SUM(new_deaths) AS total_deaths, 
    SUM(cast(new_deaths as float))/SUM(cast(new_cases as float)) AS DeathPercentage
FROM PortfolioProject..CovidDeaths
--WHERE location = 'Canada'
WHERE continent IS NOT NULL
GROUP BY date
ORDER by 1,2

-- Looking at Total Population vs Vaccinations

SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
    SUM(vac.new_vaccinations) OVER (Partition by dea.location Order by dea.location, dea.date) 
    AS RollingPeopleVaccinated
FROM PortfolioProject..CovidDeaths dea
JOIN PortfolioProject..CovidVaccinations vac
    ON dea.location = vac.location
    AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
ORDER by 2,3

-- USE CTE

WITH PopVsVac (Continent, Location, Date, Population, New_vaccinations, RollingPeopleVaccinated)
AS
(
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
    SUM(vac.new_vaccinations) OVER (Partition by dea.location Order by dea.location, dea.date) 
    AS RollingPeopleVaccinated
FROM PortfolioProject..CovidDeaths dea
JOIN PortfolioProject..CovidVaccinations vac
    ON dea.location = vac.location
    AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
--ORDER BY 2,3
)
SELECT *, (cast(RollingPeopleVaccinated as float)/cast(Population as float))*100
FROM PopVsVac

-- TEMP TABLE

DROP TABLE IF EXISTS #PercentPopulationVaccinated
CREATE TABLE #PercentPopulationVaccinated
(
    Continent NVARCHAR(255),
    Location NVARCHAR(255),
    Date DATETIME,
    Population NUMERIC,
    New_vaccinations NUMERIC,
    RollingPeopleVaccinated NUMERIC
)

INSERT INTO #PercentPopulationVaccinated
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
    SUM(vac.new_vaccinations) OVER (Partition by dea.location Order by dea.location, dea.date) 
    AS RollingPeopleVaccinated
FROM PortfolioProject..CovidDeaths dea
JOIN PortfolioProject..CovidVaccinations vac
    ON dea.location = vac.location
    AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
--ORDER BY 2,3

SELECT *, (cast(RollingPeopleVaccinated as float)/cast(Population as float))*100
FROM #PercentPopulationVaccinated

-- Creating View to store data for later visualization

CREATE VIEW PercentPopulationVaccinated AS
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
    SUM(vac.new_vaccinations) OVER (Partition by dea.location Order by dea.location, dea.date) 
    AS RollingPeopleVaccinated
FROM PortfolioProject..CovidDeaths dea
JOIN PortfolioProject..CovidVaccinations vac
    ON dea.location = vac.location
    AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
--ORDER BY 2,3